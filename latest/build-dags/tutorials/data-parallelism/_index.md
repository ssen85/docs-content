---
# metadata # 
title: Data Parallelism Pipeline
description: Learn how to build a scalable inference pipeline using data parallelism.
date: 
# taxonomy #
tags: ["integrations", "automl", "mljar", "inference"]
series:
seriesPart:
weight: 4
beta: false 
---
In this tutorial, we’ll build a scalable inference data parallelism pipeline for breast cancer detection using [data parallelism](/{{%release%}}/learn/glossary/data-parallelism). 

## Before You Start 

- You must have a {{% productName %}} cluster up and running
- You should have some basic familiarity with {{% productName %}} [pipeline specs](/{{%release%}}/build-dags/pipeline-spec) -- see the [Transform](/{{%release%}}/build-dags/pipeline-spec/transform), [Cross Input](/{{%release%}}/build-dags/pipeline-spec/input-cross), [Resource Limits](/{{%release%}}/build-dags/pipeline-spec/resource-limits), [Resource Requests](/{{%release%}}/build-dags/pipeline-spec/resource-request), and [Parallelism](/{{%release%}}/build-dags/pipeline-spec/parallelism) sections in particular

## Tutorial

Our Docker image's [user code](/{{%release%}}/learn/glossary/user-code) for this tutorial is built on top of the [pytorch/pytorch](https://github.com/civisanalytics/datascience-python) base image, which includes necessary dependencies. The underlying code and pre-trained breast cancer detection model comes from [this repo](https://github.com/nyukat/breast_cancer_classifier), developed by the Center of Data Science and Department of Radiology at NYU. Their original paper can be found [here](https://ieeexplore.ieee.org/document/8861376).

### 1. Create a Project & Input Repos

{{<stack type="wizard">}}
{{% wizardRow id="Tool"%}}
{{% wizardButton option="Pachctl CLI" state="active" %}}
{{% wizardButton option="Console" %}}
{{% /wizardRow %}}
{{% wizardResults %}}
{{% wizardResult val1="tool/pachctl-cli"%}}

1. Create a project named `data-parallelism-tutorial`. 
   ```s
   pachctl create project data-parallelism-tutorial
   ```
2. Set the project as current. 
   ```s
   pachctl config update context --project data-parallelism-tutorial
   ```
3. Create the following repos:

   ```s
   pachctl create repo models
   pachctl create repo sample_data
   ```

{{% /wizardResult %}}

{{% wizardResult val1="tool/console"%}}

1. Navigate to Console. 
2. Select **Create Project**.
3. Provide a project **Name** and **Description**.
    - **Name**: `data-parallelism-tutorial`
    - **Description**: `My fourth project tutorial.`
4. Select **Create**.
5. Scroll to the project's row and select **View Project**.
6. Select **Create Your First Repo**.
7. Provide a repo **Name** and **Description**.
    - **Name**: `models`
    - **Description**: `Repo for initial models`
8. Select **Create**.
9. Create another repo named `sample_data`.

{{% /wizardResult %}}
{{% /wizardResults  %}}
{{</stack>}}



### 2. Create a Classification Pipeline

We're going to need to first build a pipeline that will classify the breast cancer images. We'll use a [cross input](/{{%release%}}/build-dags/pipeline-spec/input-cross) to combine the sample data and models.

{{<stack type="wizard">}}
{{% wizardRow id="Tool"%}}
{{% wizardButton option="Pachctl CLI" state="active" %}}
{{% wizardButton option="Console" %}}
{{% /wizardRow %}}
{{% wizardResults %}}
{{% wizardResult val1="tool/pachctl-cli"%}}

1. Create a file named `bc_classification.json` with the following contents:
   
```s
 {
  "pipeline": {
    "name": "bc_classification",
    "project": {
      "name": "data-parallelism-tutorial"
    },
  },
  "description": "Run breast cancer classification.",
  "input": {
    "cross": [
      {
        "pfs": {
          "repo": "sample_data",
          "glob": "/*"
        }
      },
      {
        "pfs": {
          "repo": "models",
          "glob": "/"
        }
      }
    ]
  },
  "transform": {
    "cmd": [
      "/bin/bash", "run.sh", "gpu"
    ],
    "image": "pachyderm/breast_cancer_classifier:1.11.6"
  },
  "resourceLimits": {
    "gpu": {
      "type": "nvidia.com/gpu",
      "number": 1
    }
  },
  "resourceRequests": {
    "memory": "4G",
    "cpu": 1
  },
  "parallelismSpec": {
    "constant": 8
  }
}
```

2. Save the file.
3. Create the pipeline.
   ```s
   pachctl create pipeline -f /path/to/bc_classification.json
   ```

{{% /wizardResult %}}

{{% wizardResult val1="tool/console"%}}

1. Select **Create** > **Pipeline**.
2. Overwrite the default json with the following:
   ```s
    {
     "pipeline": {
       "name": "bc_classification",
       "project": {
         "name": "data-parallelism-tutorial"
       },
     },
     "description": "Run breast cancer classification.",
     "input": {
       "cross": [
         {
           "pfs": {
             "repo": "sample_data",
             "glob": "/*"
           }
         },
         {
           "pfs": {
             "repo": "models",
             "glob": "/"
           }
         }
       ]
     },
     "transform": {
       "cmd": [
         "/bin/bash", "run.sh", "gpu"
       ],
       "image": "pachyderm/breast_cancer_classifier:1.11.6"
     },
     "resourceLimits": {
       "gpu": {
         "type": "nvidia.com/gpu",
         "number": 1
       }
     },
     "resourceRequests": {
       "memory": "4G",
       "cpu": 1
     },
     "parallelismSpec": {
       "constant": 8
     }
   }
   ```

{{% /wizardResult %}}
{{% /wizardResults  %}}
{{</stack>}}


{{% notice tip %}}
#### Datum Shape 

When you define a [glob pattern](/{{%release%}}/learn/glossary/glob-pattern) in your pipeline, you are defining how {{%productName%}} should split the data so that the code can execute as parallel jobs without having to modify the underlying implementation. 

In this case, we are treating each exam (4 images and a list file) as a single datum. Each datum is processed individually, allowing parallelized computation for each exam that is added. The file structure for our `sample_data` is organized as follows:

```
sample_data/
├── <unique_exam_id>
│   ├── L_CC.png
│   ├── L_MLO.png
│   ├── R_CC.png
│   ├── R_MLO.png
│   └── gen_exam_list_before_cropping.pkl
├── <unique_exam_id>
│   ├── L_CC.png
│   ├── L_MLO.png
│   ├── R_CC.png
│   ├── R_MLO.png
│   └── gen_exam_list_before_cropping.pkl
...
```

The `gen_exam_list_before_cropping.pkl` is a pickled version of the image list, a requirement of the underlying library being used.
{{% /notice%}}

### 3. Upload Dataset 

1. Open or download this github repo.
   ```s
   gh repo clone pachyderm/docs-content
   ```
2. Navigate to this tutorial. 
   ```s
   cd content/{{%release%}}/build-dags/tutorials/data-parallelism
   ```
3. Upload the `sample_data` and `models` folders to your repos.

   {{<stack type="wizard">}}
   {{% wizardRow id="Tool"%}}
   {{% wizardButton option="Pachctl CLI" state="active" %}}
   {{% wizardButton option="Console" %}}
   {{% /wizardRow %}}
   {{% wizardResults %}}
   {{% wizardResult val1="tool/pachctl-cli"%}}

   ```s
   pachctl put file -r sample_data@master -f sample_data/
   pachctl put file -r models@master -f models/
   ```

   {{% /wizardResult %}}

   {{% wizardResult val1="tool/console"%}}

   1. Select the **sample_data** repo > **Upload Files**.
   2. Select **Browse Files**.
   3. Choose the `sample_data` directory.
   4. Select **Upload**.
   5. Select the **models** repo > **Upload Files**.
   6. Select **Browse Files**.
   7. Choose the `models` directory.
   8. Select **Upload**.
   {{% /wizardResult %}}
   {{% /wizardResults  %}}
   {{</stack>}}

---

## User Code Assets

The [Docker image](/{{%release%}}/build-dags/tutorials/user-code) used in this tutorial was built with the following assets:

{{< stack type="wizard" >}}

{{% wizardRow id ="assets" %}}
{{% wizardButton option="Dockerfile" state="active" %}}
{{% wizardButton option="Run.sh" %}}
{{% /wizardRow %}}

{{% wizardResults  %}}
{{% wizardResult val1="assets/dockerfile"%}}
```s
FROM pytorch/pytorch:1.7.1-cuda11.0-cudnn8-devel

# Update NVIDIA's apt-key
# Announcement: https://forums.developer.nvidia.com/t/notice-cuda-linux-repository-key-rotation/212772
ENV DISTRO ubuntu1804
ENV CPU_ARCH x86_64
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/$DISTRO/$CPU_ARCH/3bf863cc.pub

RUN apt-get update && apt-get install -y git libgl1-mesa-glx libglib2.0-0

WORKDIR /workspace
RUN git clone https://github.com/jimmywhitaker/breast_cancer_classifier.git /workspace
RUN pip install --upgrade pip && pip install -r requirements.txt
RUN pip install matplotlib --ignore-installed

RUN apt-get -y install tree

COPY . /workspace

```
{{% /wizardResult %}}

{{% wizardResult val1="assets/run.sh"%}}
```s
 #!/bin/bash

NUM_PROCESSES=10
DEVICE_TYPE=$1
NUM_EPOCHS=10
HEATMAP_BATCH_SIZE=100
GPU_NUMBER=0

ID=$(ls /pfs/sample_data/ | head -n 1)
DATA_FOLDER="/pfs/sample_data/${ID}/"
INITIAL_EXAM_LIST_PATH="/pfs/sample_data/${ID}/gen_exam_list_before_cropping.pkl"
PATCH_MODEL_PATH="/pfs/models/sample_patch_model.p"
IMAGE_MODEL_PATH="/pfs/models/sample_image_model.p"
IMAGEHEATMAPS_MODEL_PATH="/pfs/models/sample_imageheatmaps_model.p"

CROPPED_IMAGE_PATH="/pfs/out/${ID}/cropped_images"
CROPPED_EXAM_LIST_PATH="/pfs/out/${ID}/cropped_images/cropped_exam_list.pkl"
EXAM_LIST_PATH="/pfs/out/${ID}/data.pkl"
HEATMAPS_PATH="/pfs/out/${ID}/heatmaps"
IMAGE_PREDICTIONS_PATH="/pfs/out/${ID}/image_predictions.csv"
IMAGEHEATMAPS_PREDICTIONS_PATH="/pfs/out/${ID}/imageheatmaps_predictions.csv"
export PYTHONPATH=$(pwd):$PYTHONPATH

echo 'Stage 1: Crop Mammograms'
python3 src/cropping/crop_mammogram.py \
    --input-data-folder $DATA_FOLDER \
    --output-data-folder $CROPPED_IMAGE_PATH \
    --exam-list-path $INITIAL_EXAM_LIST_PATH  \
    --cropped-exam-list-path $CROPPED_EXAM_LIST_PATH  \
    --num-processes $NUM_PROCESSES

echo 'Stage 2: Extract Centers'
python3 src/optimal_centers/get_optimal_centers.py \
    --cropped-exam-list-path $CROPPED_EXAM_LIST_PATH \
    --data-prefix $CROPPED_IMAGE_PATH \
    --output-exam-list-path $EXAM_LIST_PATH \
    --num-processes $NUM_PROCESSES

echo 'Stage 3: Generate Heatmaps'
python3 src/heatmaps/run_producer.py \
    --model-path $PATCH_MODEL_PATH \
    --data-path $EXAM_LIST_PATH \
    --image-path $CROPPED_IMAGE_PATH \
    --batch-size $HEATMAP_BATCH_SIZE \
    --output-heatmap-path $HEATMAPS_PATH \
    --device-type $DEVICE_TYPE \
    --gpu-number $GPU_NUMBER

echo 'Stage 4a: Run Classifier (Image)'
python3 src/modeling/run_model.py \
    --model-path $IMAGE_MODEL_PATH \
    --data-path $EXAM_LIST_PATH \
    --image-path $CROPPED_IMAGE_PATH \
    --output-path $IMAGE_PREDICTIONS_PATH \
    --use-augmentation \
    --num-epochs $NUM_EPOCHS \
    --device-type $DEVICE_TYPE \
    --gpu-number $GPU_NUMBER

echo 'Stage 4b: Run Classifier (Image+Heatmaps)'
python3 src/modeling/run_model.py \
    --model-path $IMAGEHEATMAPS_MODEL_PATH \
    --data-path $EXAM_LIST_PATH \
    --image-path $CROPPED_IMAGE_PATH \
    --output-path $IMAGEHEATMAPS_PREDICTIONS_PATH \
    --use-heatmaps \
    --heatmaps-path $HEATMAPS_PATH \
    --use-augmentation \
    --num-epochs $NUM_EPOCHS \
    --device-type $DEVICE_TYPE \
    --gpu-number $GPU_NUMBER

```
{{% /wizardResult %}}

{{% /wizardResults %}}

{{< /stack>}}
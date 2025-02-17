---
# metadata # 
title:  Reprocess Spec PPS
description: Define the reprocessing behavior of a repo upon receiving new or modified. data. 
date: 
# taxonomy #
tags: ["pipelines", "pps"]
series: ["pps"]
seriesPart:
label: optional
weight: 1
---

## Spec
This is a top-level attribute of the pipeline spec. 

```s
{
    "pipeline": {...},
    "transform": {...},
    "reprocess_spec": string,
    ...
}
```

## Behavior 


`"reprocess_spec": "until_success"` is the default behavior. To mitigate datums failing for transient connection reasons, {{% productName %}} automatically [retries user code three (3) times before marking a datum as failed](/{{%release%}}/debug/pipelines). Additionally, you can [set the  `datum_tries`](/{{%release%}}/build-dags/pipeline-spec/datum-tries) field to determine the number of times a job attempts to run on a datum when a failure occurs.

Let's compare `"until_success"` and `"every_job"`:

Say we have 2 identical pipelines (`reprocess_until_success.json` and `reprocess_at_every_job.json`) but for the `"reprocess_spec"` field set to `"every_job"` in `reprocess_at_every_job.json`. 

Both use the same input repo and have a glob pattern set to `/*`. 

- When adding 3 text files to the input repo (file1.txt, file2.txt, file3.txt), the 2 pipelines (reprocess_until_success and reprocess_at_every_job) will process the 3 datums (here, the glob pattern `/*` creates one datum per file).
- Now, let's add a 4th file file4.txt to our input repo or modify the content of file2.txt for example.
    - **Case of our default `reprocess_until_success.json pipeline`**: A quick check at the list datum on the job id shows 4 datums, of which 3 were skipped. (Only the changed file was processed)
    - **Case of `reprocess_at_every_job.json`**: A quick check at the list datum on the job id shows that all 4 datums were reprocessed, none were skipped.


{{% notice warning %}}
`"reprocess_spec": "every_job` will not take advantage of {{% productName %}}'s default de-duplication. In effect, this can lead to slower pipeline performance. Before using this setting, consider other options such as including metadata in your file, naming your files with a timestamp, UUID, or other unique identifiers in order to take advantage of de-duplication.
{{% /notice %}}

## When to Use 

Per default, {{% productName %}} avoids repeated processing of unchanged datums (i.e., it processes only the datums that have changed and skip the unchanged datums). This incremental behavior ensures efficient resource utilization. However, you might need to alter this behavior for specific use cases and **force the reprocessing of all of your datums systematically**. This is especially useful when your pipeline makes an external call to other resources, such as a deployment or triggering an external pipeline system.  Set `"reprocess_spec": "every_job"` in order to enable this behavior. 
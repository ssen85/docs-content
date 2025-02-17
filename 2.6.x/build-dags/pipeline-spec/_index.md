---
# metadata # 
title:  Pipeline Specification (PPS)
description:  Learn about the different attributes of a pipeline spec. 
date: 
# taxonomy #
tags: ["pipeline", "pipelines", "pipeline specification", "pps", "pipeline spec" ]
series:
seriesPart:

weight: 2
---


This document discusses each of the fields present in a pipeline specification.


## Before You Start 
- {{% productName %}}'s pipeline specifications can be written in JSON or YAML.
- {{% productName %}} uses its json parser if the first character is `{`.
- A pipeline specification file can contain multiple pipeline declarations at once.

## Minimal Spec 

Generally speaking, the only attributes that are strictly required for all scenarios are `pipeline.name` and [`transform`](transform). Beyond those, other attributes are conditionally required based on your pipeline's use case. The following are a few examples of common use cases along with their minimally required attributes.

{{< stack type="wizard">}}

{{% wizardRow id="Use Case"%}}
{{% wizardButton option="Cron" state="active" %}}
{{% wizardButton option="Egress (DB)" %}}
{{% wizardButton option="Egress (Storage)" %}}
{{% wizardButton option="Input" %}}
{{% wizardButton option="Service" %}}
{{% wizardButton option="Spout" %}}
{{% wizardButton option="S3" %}}
{{% wizardButton option="Full" %}}

{{% /wizardRow %}}

{{% wizardResults  %}}
{{% wizardResult val1="use-case/cron"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
    "cron": {
      {
          "name": string,
          "spec": string,
          "repo": string,
          "start": time,
          "overwrite": bool
      }
    }
  }
}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/egress-db"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
        "pfs": {
            "repo": "data",
            "glob": "/*"
        }
    },
  "egress": {
      "sql_database": {
          "url": string,
          "file_format": {
              "type": string,
              "columns": [string]
          },
          "secret": {
              "name": string,
              "key": "PACHYDERM_SQL_PASSWORD"
          }
      }
    },

}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/egress-storage"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
        "pfs": {
            "repo": "data",
            "glob": "/*"
        }
    },
  "egress": {
      "URL": "s3://bucket/dir"
    },

}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/input"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
        "pfs": {
            "repo": "data",
            "glob": "/*"
        }
    }
}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/service"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
        "pfs": {
            "repo": "data",
            "glob": "/*"
        }
    },
  "service": {
      "internal_port": int,
      "external_port": int
    },
}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/spout"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "spout": {
  },
}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/s3"%}}
```s
{
  "pipeline": {
    "name": "wordcount",
    "project": {
      name: "projectName"
    }
  },
  "transform": {
    "image": "wordcount-image",
    "cmd": ["/binary", "/pfs/data", "/pfs/out"]
  },
  "input": {
        "pfs": {
            "repo": "data",
            "glob": "/*"
        }
    },
  "s3_out": true,
}
```
{{% /wizardResult %}}

{{% wizardResult val1="use-case/full" %}}
```s
{
  "pipeline": {
    "name": string,
    "project": {
      name: "projectName"
    },
  },
  "description": string,
  "metadata": {
    "annotations": {
        "annotation": string
    },
    "labels": {
        "label": string
    }
  },
  "tf_job": {
    "tf_job": string,
  },
  "transform": {
    "image": string,
    "cmd": [ string ],
    "err_cmd": [ string ],
    "env": {
        string: string
    },
    "secrets": [ {
        "name": string,
        "mount_path": string
    },
    {
        "name": string,
        "env_var": string,
        "key": string
    } ],
    "image_pull_secrets": [ string ],
    "stdin": [ string ],
    "err_stdin": [ string ],
    "accept_return_code": [ int ],
    "debug": bool,
    "user": string,
    "working_dir": string,
    "dockerfile": string,
    "memory_volume": bool,
  },
  "parallelism_spec": {
    "constant": int
  },
  "egress": {
    // Egress to an object store
    "URL": "s3://bucket/dir"
    // Egress to a database
    "sql_database": {
        "url": string,
        "file_format": {
            "type": string,
            "columns": [string]
        },
        "secret": {
            "name": string,
            "key": "PACHYDERM_SQL_PASSWORD"
        }
    }
  },
  "update": bool,
  "output_branch": string,
  [
    {
      "worker_id": string,
      "job_id": string,
      "datum_status" : {
        "started": timestamp,
        "data": []
      }
    }
  ],
  "s3_out": bool,
  "resource_requests": {
    "cpu": number,
    "memory": string,
    "gpu": {
      "type": string,
      "number": int
    }
    "disk": string,
  },
  "resource_limits": {
    "cpu": number,
    "memory": string,
    "gpu": {
      "type": string,
      "number": int
    }
    "disk": string,
  },
  "sidecar_resource_limits": {
    "cpu": number,
    "memory": string,
    "gpu": {
      "type": string,
      "number": int
    }
    "disk": string,
  },
  "input": {
    <"pfs", "cross", "union", "join", "group" or "cron" see below>
  },
  "description": string,
  "reprocess": bool,
  "service": {
    "internal_port": int,
    "external_port": int
  },
  "spout": {
    \\ Optionally, you can combine a spout with a service:
    "service": {
      "internal_port": int,
      "external_port": int
    }
  },
  "datum_set_spec": {
    "number": int,
    "size_bytes": int,
    "per_worker": int,
  }
  "datum_timeout": string,
  "job_timeout": string,
  "salt": string,
  "datum_tries": int,
  "scheduling_spec": {
    "node_selector": {string: string},
    "priority_class_name": string
  },
  "pod_spec": string,
  "pod_patch": string,
  "spec_commit": {
    "option": false,
    "branch": {
      "option": false,
      "repo": {
        "option": false,
        "name": string,
        "type": string,
        "project":{
          "option": false,
          "name": string,
        },
      },
      "name": string
    },
    "id": string,
  }
  "metadata": {

  },
  "reprocess_spec": string,
  "autoscaling": bool
}

------------------------------------
"pfs" input
------------------------------------

"pfs": {
  "name": string,
  "repo": string,
  "repo_type":string,
  "branch": string,
  "commit":string,
  "glob": string,
  "join_on":string,
  "outer_join": bool,
  "group_by": string,
  "lazy" bool,
  "empty_files": bool,
  "s3": bool,
  "trigger": {
    "branch": string,
    "all": bool,
    "cron_spec": string,
  },
}

------------------------------------
"cross" or "union" input
------------------------------------

"cross" or "union": [
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "lazy" bool,
      "empty_files": bool,
      "s3": bool
    }
  },
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "lazy" bool,
      "empty_files": bool,
      "s3": bool
    }
  }
  ...
]


------------------------------------
"join" input
------------------------------------

"join": [
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "join_on": string,
      "outer_join": bool,
      "lazy": bool,
      "empty_files": bool,
      "s3": bool
    }
  },
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "join_on": string,
      "outer_join": bool,
      "lazy": bool,
      "empty_files": bool,
      "s3": bool
    }
  }
]


------------------------------------
"group" input
------------------------------------

"group": [
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "group_by": string,
      "lazy": bool,
      "empty_files": bool,
      "s3": bool
    }
  },
  {
    "pfs": {
      "name": string,
      "repo": string,
      "branch": string,
      "glob": string,
      "group_by": string,
      "lazy": bool,
      "empty_files": bool,
      "s3": bool
    }
  }
]



------------------------------------
"cron" input
------------------------------------

"cron": {
    "name": string,
    "spec": string,
    "repo": string,
    "start": time,
    "overwrite": bool
}
```
{{% /wizardResult%}}

{{% /wizardResults %}}

{{</stack>}}

{{% notice note %}}
For a single-page view of all PPS options, go to the [PPS series page](/series/pps).
{{% /notice %}}
---
title: Spout Pipelines
description: Learn how to create pipelines using the pachyderm-sdk analogs for creating pipelines.
directory: true 
---

This example is a reproduction of the Spouts101 example from the Pachyderm repo. This example uses the `pachyderm-sdk` analogs for creating pipelines (`spout.py`), which was done using `pachctl` commands in the Spouts101 example. For more information on Spouts, a full walkthrough of the original example, or the pipelines' user code, go *[here](https://github.com/pachyderm/pachyderm/tree/master/examples/spouts/spout101)*.

## Before You Start 
- You must have a running [{{%productName%}} cluster](https://docs.pachyderm.com/latest/get-started/)
- You must have installed the latest package of `pachyderm-sdk`


## How to Create a Spout Pipeline

{{%notice tip%}}
You can download this repo at `pachyderm/docs-content` and navigate to `latest/sdk/examples/spout` to execute the following steps.
{{% /notice %}}

1. Save the following code as `spout.py`:

   ```python
   from pachyderm_sdk import Client
   from pachyderm_sdk.api import pps


   def main(client: Client):
       spout = pps.Pipeline(name="spout")
       client.pps.create_pipeline(
           pipeline=spout,
           transform=pps.Transform(
               cmd=["python", "consumer/main.py"],
               image="pachyderm/example-spout101:2.0.1",
           ),
           spout=pps.Spout(),
           description="A spout pipeline that emulates the reception of data from an external source",
       )

       processor = pps.Pipeline(name="processor")
       client.pps.create_pipeline(
           pipeline=processor,
           transform=pps.Transform(
               cmd=["python", "processor/main.py"],
               image="pachyderm/example-spout101:2.0.1",
           ),
           input=pps.Input(
               pfs=pps.PfsInput(repo="spout", branch="master", glob="/*"),
           ),
           description="A pipeline that sorts 1KB vs 2KB files",
       )

       reducer = pps.Pipeline(name="reducer")
       client.pps.create_pipeline(
           pipeline=reducer,
           transform=pps.Transform(
               cmd=["bash"],
               stdin=[
                   "set -x",
                   "FILES=/pfs/processor/*/*",
                   "for f in $FILES",
                   "do",
                   "directory=`dirname $f`",
                   "out=`basename $directory`",
                   "cat $f >> /pfs/out/${out}.txt",
                   "done",
               ],
           ),
           input=pps.Input(
               pfs=pps.PfsInput(repo="processor", branch="master", glob="/*"),
           ),
           description="A pipeline that reduces 1K/ and 2K/ directories",
       )


   if __name__ == "__main__":
       # Connects to a pachyderm cluster using the pachctl config file located
       # at ~/.pachyderm/config.json. For other setups, you'll want one of the 
       # alternatives:
       # 1) To connect to pachyderm when this script is running inside the
       #    cluster, use `Client.new_in_cluster()`.
       # 2) To connect to pachyderm via a pachd address, use
       #    `Client.new_from_pachd_address`.
       # 3) To explicitly set the host and port, pass parameters into
       #    `Client()`.
       # 4) To use a config file located elsewhere, pass in the path to that
       #    config file to Client.from_config()
       client = Client.from_config()
       main(client)

   ```

2. Run the script:

   ```shell
   python spout.py
   ```
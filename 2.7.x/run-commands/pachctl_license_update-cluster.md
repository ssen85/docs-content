---
date: 2023-09-07T13:28:03-04:00
title: "pachctl license update-cluster"
description: "Learn about the pachctl_license_update-cluster command"
---

## pachctl license update-cluster

Update an existing cluster registered with the license server.

### Synopsis

Update an existing cluster registered with the license server.

```
pachctl license update-cluster [flags]
```

### Options

```
      --address string                 The host and port where the cluster can be reached by the enterprise server
      --cluster-deployment-id string   The deployment id of the updated cluster
  -h, --help                           help for update-cluster
      --id string                      The id for the cluster to update
      --user-address string            The host and port where the cluster can be reached by a user
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl license](../pachctl_license)	 - License commmands manage the Enterprise License service


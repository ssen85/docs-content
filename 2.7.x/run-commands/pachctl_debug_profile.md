---
date: 2023-09-07T13:28:03-04:00
title: "pachctl debug profile"
description: "Learn about the pachctl_debug_profile command"
---

## pachctl debug profile

Collect a set of pprof profiles.

### Synopsis

Collect a set of pprof profiles.

```
pachctl debug profile <profile> <file> [flags]
```

### Options

```
  -d, --duration duration   Duration to run a CPU profile for. (default 1m0s)
  -h, --help                help for profile
      --pachd               Only collect the profile from pachd.
  -p, --pipeline string     Only collect the profile from the worker pods for the given pipeline.
  -w, --worker string       Only collect the profile from the given worker pod.
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl debug](../pachctl_debug)	 - Debug commands for analyzing a running cluster.


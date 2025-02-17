---
date: 2023-10-18T16:51:53-04:00
title: "pachctl version"
description: "Learn about the pachctl version command"
---

## pachctl version

Print Pachyderm version information.

### Synopsis

Print Pachyderm version information.

```
pachctl version [flags]
```

### Options

```
      --client-only      If set, only print pachctl's version, but don't make any RPCs to pachd. Useful if pachd is unavailable
      --enterprise       If set, 'pachctl version' will run on the active enterprise context.
  -h, --help             help for version
  -o, --output string    Output format when --raw is set: "json" or "yaml" (default "json")
      --raw              Disable pretty printing; serialize data structures to an encoding such as json or yaml
      --timeout string   If set, 'pachctl version' will timeout after the given duration (formatted as a golang time duration--a number followed by ns, us, ms, s, m, or h). If --client-only is set, this flag is ignored. If unset, pachctl will use a default timeout; if set to 0s, the call will never time out. (default "default")
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl](../pachctl)	 - 


---
date: 2023-09-07T13:28:03-04:00
title: "pachctl mount"
description: "Learn about the pachctl_mount command"
---

## pachctl mount

Mount pfs locally. This command blocks.

### Synopsis

Mount pfs locally. This command blocks.

```
pachctl mount <path/to/mount/point> [flags]
```

### Options

```
  -d, --debug            Turn on debug messages.
  -h, --help             help for mount
      --project string   Project in which repo is located. (default "default")
  -r, --repos []string   Repos and branches / commits to mount, arguments should be of the form "repo[@branch=commit][+w]", where the trailing flag "+w" indicates write. You can omit the branch when specifying a commit unless the same commit ID is on multiple branches in the repo. (default [])
  -w, --write            Allow writing to pfs through the mount.
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl](../pachctl)	 - 


---
date: 2023-09-07T13:28:03-04:00
title: "pachctl auth logout"
description: "Learn about the pachctl_auth_logout command"
---

## pachctl auth logout

Log out of Pachyderm by deleting your local credential

### Synopsis

Log out of Pachyderm by deleting your local credential. Note that it's not necessary to log out before logging in with another account (simply run 'pachctl auth login' twice) but 'logout' can be useful on shared workstations.

```
pachctl auth logout [flags]
```

### Options

```
      --enterprise   Log out of the active enterprise context
  -h, --help         help for logout
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl auth](../pachctl_auth)	 - Auth commands manage access to data in a Pachyderm cluster


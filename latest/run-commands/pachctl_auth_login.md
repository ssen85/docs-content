---
date: 2023-10-18T16:51:53-04:00
title: "pachctl auth login"
description: "Learn about the pachctl auth login command"
---

## pachctl auth login

Log in to Pachyderm

### Synopsis

This command Logs in to Pachyderm. Any resources that have been restricted to the account you have with your ID provider (e.g. GitHub, Okta) account will subsequently be accessible.

```
pachctl auth login [flags]
```

### Options

```
      --enterprise   Login for the active enterprise context.
  -h, --help         help for login
  -t, --id-token     If set, read an ID token on stdin to authenticate the user
  -b, --no-browser   If set, don't try to open a web browser
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl auth](../pachctl_auth)	 - Auth commands manage access to data in a Pachyderm cluster


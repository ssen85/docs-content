---
date: 2023-09-07T13:28:03-04:00
title: "pachctl auth revoke"
description: "Learn about the pachctl_auth_revoke command"
---

## pachctl auth revoke

Revoke a Pachyderm auth token

### Synopsis

Revoke a Pachyderm auth token.

```
pachctl auth revoke [flags]
```

### Options

```
      --enterprise     Revoke an auth token (or all auth tokens minted for one user) on the enterprise server
  -h, --help           help for revoke
      --token string   Pachyderm auth token that should be revoked (one of --token or --user must be set)
      --user string    User whose Pachyderm auth tokens should be revoked (one of --token or --user must be set)
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl auth](../pachctl_auth)	 - Auth commands manage access to data in a Pachyderm cluster


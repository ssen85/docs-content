---
date: 2023-09-07T13:28:03-04:00
title: "pachctl delete file"
description: "Learn about the pachctl_delete_file command"
---

## pachctl delete file

Delete a file.

### Synopsis

This command deletes a file.

```
pachctl delete file <repo>@<branch-or-commit>:<path/in/pfs> [flags]
```

### Examples

```
 pachctl delete file foo@bar:image.png 
 pachctl delete file foo@0001a0100b1c10d01111e001fg00h00i:/images/image.png 
 pachctl delete file -r foo@master:/images 
 pachctl delete file -r foo@master:/images --project projectContainingFoo 

```

### Options

```
  -h, --help             help for file
      --project string   Specify the project (by name) where the file's repo is located. (default "video-to-frame-traces")
  -r, --recursive        Recursively delete the files in a directory.
```

### Options inherited from parent commands

```
      --no-color   Turn off colors.
  -v, --verbose    Output verbose logs
```

### SEE ALSO

* [pachctl delete](../pachctl_delete)	 - Delete an existing Pachyderm resource.


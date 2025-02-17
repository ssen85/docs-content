---
# metadata # 
title:  PGBouncer HCVs
description: Deploy a lightweight connection pooler for PostgreSQL.
date: 
# taxonomy #
tags: ["helm"]
series: ["helm"]
seriesPart:
weight: 9 
---

## About

The PGBouncer section configures a PGBouncer Postgres connection pooler.

## Values

The following section contains a series of tabs for commonly used configurations for this section of your values.yml Helm chart. 

```s

pgbouncer:
  service:
    type: ClusterIP # defines the Kubernetes service type.
  annotations: {}
  priorityClassName: ""
  nodeSelector: {}
  tolerations: []
  image:
    repository: pachyderm/pgbouncer
    tag: 1.16.1-debian-10-r82
  resources: # defines resources in standard kubernetes format; unset by default.
    {}

    #limits:
    #  cpu: "1"
    #  memory: "2G"
    #requests:
    #  cpu: "1"
    #  memory: "2G"

  maxConnections: 10000 # defines the maximum number of concurrent connections into pgbouncer.
  defaultPoolSize: 80 # specifies the maximum number of concurrent connections from pgbouncer to the postgresql database.
```


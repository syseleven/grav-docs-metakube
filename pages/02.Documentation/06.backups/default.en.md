---
title: 'Backups'
published: true
taxonomy:
    tag:
        - backup
        - kubernetes
        - cli
---

## Kubernetes cluster state backups

Every MetaKube Kubernetes cluster comes with [Heptio Ark](https://heptio.github.io/ark/) installed and configured automatically.

Ark gives you tools to back up and restore your Kubernetes cluster resources and persistent volumes. Which means you can take backups
of your cluster and restore in case of loss or replicate your production environment for development and testing environments.

Backups are automatically stored in a [SysEleven Stack Object Storage](https://docs.syseleven.de/syseleven-stack/en/documentation/object-storage) bucket on a different cloud region then your cluster.

This [Tutorial](../../03.Tutorials/18.create-backup-and-restore/default.en.md) gives you an example how to use Ak to create a backup and restore from it.
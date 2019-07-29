---
title: Backups
published: true
taxonomy:
    tag:
        - backup
        - kubernetes
        - cli
---

## Kubernetes cluster state backups

Every MetaKube Kubernetes cluster comes with [Velero](https://heptio.github.io/velero/) installed and configured automatically. Velero gives you tools to back up and restore your Kubernetes cluster resources. Which means you can take backups of your cluster and restore in case of loss or replicate your production environment for development and testing environments. Backups are automatically stored in a [SysEleven Stack Object Storage](https://docs.syseleven.de/syseleven-stack/en/reference/object-storage) bucket on a different cloud region than your cluster.
[This Tutorial](../../04.tutorials/18.create-backup-and-restore/default.en.md) gives you an example how to use Velero to create a backup and restore from it.

### Deleting backups after cluster deletion

If a Kubernetes cluster is deleted, the backups created for this will remain in the SysEleven Stack Object Storage. If you are sure that you don't need these backups anymore, you can delete them manually:
Install and configure [S3cmd](https://s3tools.org/s3cmd) as described in the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/en/reference/object-storage). The region is `s3.cbk.cloud.syseleven.net`. Your credentials can be retrieved with `openstack ec2 credentials list`. We create one S3 bucket for every cluster. To list all available buckets run:

```shell
s3cmd ls
```

Pick the buckets with the cluster id, that you want to delete and run:

```shell
s3cmd rb s3://metakube-cluster-backup-${clusterId} --recursive
```

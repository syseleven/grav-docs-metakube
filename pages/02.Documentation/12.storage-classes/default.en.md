---
title: 'Storage Classes'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - storage
        - kubernetes
        - cli
---

In order to use persistent storage over a [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) in your cluster, a [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/) needs to be specified there. MetaKube clusters already come with a default StorageClass for our [SysEleven Stack Block Storage](https://docs.syseleven.de/syseleven-stack/en/reference/block-storage) in OpenStack and [Elastic Block Store](https://aws.amazon.com/en/ebs/) on AWS as the default StorageClass built-in:

```bash
$ kubectl get storageclasses
NAME                      PROVISIONER            AGE
sys11-quobyte (default)   kubernetes.io/cinder   4d
```

or

```bash
$ kubectl get storageclasses
NAME                      PROVISIONER            AGE
sys11-aws (default)       kubernetes.io/aws-ebs  4d
```

This means you can use this StorageClass directly in a PersistentVolumeClaim to request a block storage Volume:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: your-persistent-volme-claim
  namespace: your-namespace
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

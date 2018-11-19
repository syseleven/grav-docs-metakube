---
title: 'Using ReadWriteMany Volumes'
published: true
taxonomy:
    tag:
        - storage
        - kubernetes
---

The default MetaKube [Storage Class](../../02.Documentation/10.storage-classes/default.en.md) which uses the OpenStack Cinder API does only support ReadWriteOnce volumes. This means that one volume can only be mounted in one container at the same time. While this is usually fine and you should use databases or [Object Storage](https://docs.syseleven.de/syseleven-stack/en/documentation/object-storage) if you need to read and write from multiple instances, there are some use cases, especially in legacy software, where ReadWriteMany volumes may be needed.

This tutorial shows how you can enable this, by deploying a NFS server into your cluster that provides ReadWriteMany volumes.

!!! Note that the I/O performance of these NFS powered ReadWriteMany volumes will not be the greatest since reads and writes have to go through an additional NFS server. Also this NFS server is not highly available, though the data in the underlying volume is replicated over the OpenStack cluster.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Deploy the nfs-server-provisioner

You can deploy the [nfs-server-provisioner](https://github.com/helm/charts/tree/master/stable/nfs-server-provisioner) with Helm, which is automatically included in MetaKube clusters (See [Using Helm](../17.using-helm/default.en.md)):

```shell
helm install stable/nfs-server-provisioner --name nfs-provisioner --namespace nfs --set=persistence.enabled=true,persistence.storageClass=sys11-quobyte,persistence.size=5Gi
```

This will deployed a NFS server, that stores its data on a replicated ReadWriteOnce Quobyte volume. The NFS server is exposed through an additional StorageClass called `nfs`:

```shell
$ kubectl get storageclasses nfs
NAME   PROVISIONER                                            AGE
nfs    cluster.local/nfs-provisioner-nfs-server-provisioner   2m28s
```

The deployment and mounting of the volume may take a few seconds, you can see the current state with:

```shell
$ kubectl get pods --namespace nfs
NAME                                       READY   STATUS    RESTARTS   AGE
nfs-provisioner-nfs-server-provisioner-0   1/1     Running   0          3m53s
```

## Deploy an application

After the NFS server and the new StorageClass are ready, we can now deploy an application that uses this StorageClass to create a ReadWriteMany volume.

For easy cleanups we create a new namespace for our tutorial:

```shell
$ kubectl create namespace read-write-many-tutorial
namespace/read-write-many-tutorial created
```

For our tutorial we will deploy an NGINX container with two replicas and a persistent volume named nginx-data:

```shell
cat <<'EOF' | kubectl --namespace=read-write-many-tutorial apply -f -
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: read-write-many
    spec:
      volumes:
        - name: nginx-data
          persistentVolumeClaim:
           claimName: nginx-data
      containers:
      - image: nginx:latest
        name: nginx
        ports:
        - containerPort: 80
        volumeMounts:
          - mountPath: "/data"
            name: nginx-data
            readOnly: false
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-data
  labels:
    app: read-write-many
spec:
  storageClassName: nfs
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
EOF
```

Wait until both pods are running:

```shell
$ kubectl get pods --namespace read-write-many-tutorial
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-8f75f7dbf-dtc77   1/1     Running   0          24s
nginx-deployment-8f75f7dbf-f82x6   1/1     Running   0          24s
```

If you now store data into the volume of the first pod:

```shell
kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'echo "TEST" > /data/test.txt'
```

You can read this file from both pods:

```shell
$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST

$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[1].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST
```

## Cleanup

Delete the namespace:

```shell
$ kubectl delete namespace read-write-many-tutorial
namespace "read-write-many-tutorial" deleted
```

If you also want to remove the NFS server and StorageClass:

```shell
helm del --purge nfs-provisioner
```

```shell
$ kubectl delete namespace nfs
namespace "nfs" deleted
```

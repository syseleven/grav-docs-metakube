---
title: 'Resize persistent volume'
published: true
taxonomy:
    tag:
        - storage
        - kubernetes
        - cli
---

The MetaKube [Storage Class](../../02.documentation/12.storage-classes/default.en.md) also allows resizing persistent volumes without loosing any data, as long as they are not currently in use by a pod. This tutorial shows how to do this.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Deploy an application

For easy cleanups we create a new namespace for our tutorial:

```shell
$ kubectl create namespace resize-storage-tutorial
namespace/resize-storage-tutorial created
```

For our tutorial we will deploy an NGINX container with a persistent volume named nginx-logs:

```shell
cat <<'EOF' | kubectl --namespace=resize-storage-tutorial apply -f -
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: storage
    spec:
      volumes:
        - name: nginx-logs
          persistentVolumeClaim:
           claimName: nginx-logs
      containers:
      - image: nginx:latest
        name: nginx
        ports:
        - containerPort: 80
        volumeMounts:
          - mountPath: "/var/log/nginx"
            name: nginx-logs
            readOnly: false
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-logs
  labels:
    app: resize-storage
spec:
  storageClassName: sys11-quobyte
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

Check that the NGINX pod has been created successfully and is running:

```shell
$ kubectl get pods --namespace resize-storage-tutorial
NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-78c8b7d6f6-tl29k  1/1       Running   0          1m
```

You can also check that the persistent volume claim has been created with the size of 1Gi:

```shell
$ kubectl get pvc --namespace resize-storage-tutorial
NAME         STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
nginx-logs   Bound     pvc-9b7f6820-e695-11e8-85a5-0a580af403f8   1Gi        RWO            sys11-quobyte   3m
```

There also was a persistent volume created for this claim:

```shell
$ kubectl get pv --namespace resize-storage-tutorial
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                STORAGECLASS    REASON   AGE
pvc-9b7f6820-e695-11e8-85a5-0a580af403f8   1Gi        RWO            Delete           Bound    resize-storage-tutorial/nginx-logs   sys11-quobyte            60s
```

## Resize the persistent volume

Because of limitations in Kubernetes and OpenStack volumes can not be resized while they are in use. So we first must ensure that no running pod is using the volume. The easiest way is to scale the deployment of the NGINX application down.

Note that just deleting the pod is not enough, since it will be directly rescheduled. Also you have to ensure that the volume is not attached to any running pod before resizing it.

```shell
$ kubectl scale deployment nginx-deployment --replicas 0 --namespace resize-storage-tutorial
deployment.extensions/nginx-deployment scaled
```

Wait a few seconds until all pods are terminated:

```shell
$ kubectl get pods --namespace resize-storage-tutorial
No resources found.
```

Now the capacity of the PersistentVolumeClaim can be edited:

```shell
$ kubectl patch pvc nginx-logs --namespace resize-storage-tutorial -p '{"spec":{"resources":{"requests":{"storage":"2Gi"}}}}' --type=merge
persistentvolumeclaim/nginx-logs patched
```

If you describe the PersistentVolumeClaim now, you can see that Kubernetes is waiting for a pod to restart in order to change the volume size. The size of the volume will still appear as 1Gi until the volume has been attached and the resize has been performed.

```shell
$ kubectl describe pvc nginx-logs --namespace resize-storage-tutorial
...
Conditions:
  Type                      Status  LastProbeTime                     LastTransitionTime                Reason  Message
  ----                      ------  -----------------                 ------------------                ------  -------
  FileSystemResizePending   True    Mon, 01 Jan 0001 00:00:00 +0000   Mon, 12 Nov 2018 17:14:52 +0100           Waiting for user to (re-)start a pod to finish file system resize of volume on node.
...
```

Now let's scale the deployment up again:

```shell
$ kubectl scale deployment nginx-deployment --replicas 1 --namespace resize-storage-tutorial
deployment.extensions/nginx-deployment scaled
```

After a few seconds the pod is up and running again:

```shell
$ kubectl get pods --namespace resize-storage-tutorial
NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-78c8b7d6f6-4fv5s  1/1       Running   0          1m
```

And the volume is resized:

```shell
$ kubectl get pvc --namespace resize-storage-tutorial
NAME         STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
nginx-logs   Bound     pvc-9b7f6820-e695-11e8-85a5-0a580af403f8   2Gi        RWO            sys11-quobyte   3m

$ kubectl get pv --namespace resize-storage-tutorial
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                STORAGECLASS    REASON   AGE
pvc-9b7f6820-e695-11e8-85a5-0a580af403f8   2Gi        RWO            Delete           Bound    resize-storage-tutorial/nginx-logs   sys11-quobyte            60s
```

## Clean up

Delete the namespace:

```shell
$ kubectl delete namespace resize-storage-tutorial
namespace "resize-storage-tutorial" deleted
```

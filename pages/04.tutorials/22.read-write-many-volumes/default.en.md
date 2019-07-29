---
title: 'Using ReadWriteMany Volumes'
published: true
taxonomy:
    tag:
        - kubernetes
        - storage
---

The default MetaKube [Storage Class](../../02.documentation/12.storage-classes/default.en.md) which uses the OpenStack Cinder API only supports ReadWriteOnce (RWO) volumes. This means that a volume can only be mounted in one container at the same time. While this is usually fine and you should use databases or [Object Storage](https://docs.syseleven.de/syseleven-stack/en/reference/object-storage) if you need to read and write from multiple instances, there are some use cases, especially in legacy software, where ReadWriteMany (RWX) volumes may be needed.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Rook

This tutorial shows how you can enable RWX, by deploying [rook](https://rook.io) with a shared file system.

!!! This tutorial was tested with Rook version 0.9.0. If you encounter problems, please make sure to check the [Rook documentation](https://rook.io/docs/rook/v0.9/) for breaking changes.

!!! Note that rook builds up a Ceph cluster in the background. You must have deeper knowledge about Ceph to operate a cluster in production grade clusters. We will not provide support for Ceph clusters in your MetaKube clusters!

### Known Limitations

* You should use local storage nodes (flavors beginning with `l1.`) for the rook deployment. See [manage node deployments](../09.manage-node-deployments/default.en.md) for how to add local storage nodes and [Assigning pods to nodes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) for how to limit rook and ceph to these local storage nodes.
* Rook supports only one shared file system. If you need multiple shared file systems you need to set the env `ROOK_ALLOW_MULTIPLE_FILESYSTEMS=true`. This is an experimental feature! Further information can be found in the [ceph docs](http://docs.ceph.com/docs/master/cephfs/experimental-features/#multiple-filesystems-within-a-ceph-cluster) and the [rook docs](https://rook.io/docs/rook/v0.9/ceph-filesystem.html).
* Even though it should be possible to change the rook namespaces, we observed strange issues with the Ceph cluster when using different namespaces.

### Deploy the Rook Operator

You can deploy the [rook operator](https://github.com/rook/rook) with Helm, which is automatically included in MetaKube clusters (See [Using Helm](../17.using-helm/default.en.md)):

```shell
helm repo add rook-beta https://charts.rook.io/beta
helm install --namespace rook-ceph-system rook-beta/rook-ceph --name rook-operator
```

After some minutes the rook-operator should be up and running. It consists of a ceph-operator deployment, a ceph-agent daemonset and a discover daemonset:

```shell
$ kubectl get pods -n rook-ceph-system
NAME                                 READY   STATUS    RESTARTS   AGE
rook-ceph-agent-792pc                1/1     Running   0          4m
rook-ceph-agent-ckpb2                1/1     Running   0          4m
rook-ceph-agent-qszkg                1/1     Running   0          4m
rook-ceph-operator-f4cd7f8d5-c2ww4   1/1     Running   0          4m
rook-discover-8mcxt                  1/1     Running   0          4m
rook-discover-vbd2z                  1/1     Running   0          4m
rook-discover-wrkhp                  1/1     Running   0          4m
```

### Create a Rook Cluster

Now that the Rook operator, agent, and discover pods are running, we can create the Rook cluster.

```shell
cat <<'EOF' | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
  name: rook-ceph
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: rook-ceph-osd
  namespace: rook-ceph
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: rook-ceph-mgr
  namespace: rook-ceph
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-osd
  namespace: rook-ceph
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: [ "get", "list", "watch", "create", "update", "delete" ]
---
# Aspects of ceph-mgr that require access to the system namespace
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-mgr-system
  namespace: rook-ceph
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  verbs:
  - get
  - list
  - watch
---
# Aspects of ceph-mgr that operate within the cluster's namespace
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-mgr
  namespace: rook-ceph
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - services
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - batch
  resources:
  - jobs
  verbs:
  - get
  - list
  - watch
  - create
  - update
  - delete
- apiGroups:
  - ceph.rook.io
  resources:
  - "*"
  verbs:
  - "*"
---
# Allow the operator to create resources in this cluster's namespace
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-cluster-mgmt
  namespace: rook-ceph
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: rook-ceph-cluster-mgmt
subjects:
- kind: ServiceAccount
  name: rook-ceph-system
  namespace: rook-ceph-system
---
# Allow the osd pods in this namespace to work with configmaps
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-osd
  namespace: rook-ceph
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: rook-ceph-osd
subjects:
- kind: ServiceAccount
  name: rook-ceph-osd
  namespace: rook-ceph
---
# Allow the ceph mgr to access the cluster-specific resources necessary for the mgr modules
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-mgr
  namespace: rook-ceph
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: rook-ceph-mgr
subjects:
- kind: ServiceAccount
  name: rook-ceph-mgr
  namespace: rook-ceph
---
# Allow the ceph mgr to access the rook system resources necessary for the mgr modules
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-mgr-system
  namespace: rook-ceph-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: rook-ceph-mgr-system
subjects:
- kind: ServiceAccount
  name: rook-ceph-mgr
  namespace: rook-ceph
---
# Allow the ceph mgr to access cluster-wide resources necessary for the mgr modules
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: rook-ceph-mgr-cluster
  namespace: rook-ceph
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: rook-ceph-mgr-cluster
subjects:
- kind: ServiceAccount
  name: rook-ceph-mgr
  namespace: rook-ceph
---
#################################################################################
# The Ceph Cluster CRD example
#################################################################################
apiVersion: ceph.rook.io/v1
kind: CephCluster
metadata:
  name: rook-ceph
  namespace: rook-ceph
spec:
  cephVersion:
    # For the latest ceph images, see https://hub.docker.com/r/ceph/ceph/tags
    image: ceph/ceph:v13.2.2-20181023
  dataDirHostPath: /var/lib/rook
  dashboard:
    enabled: true
  mon:
    count: 3
    allowMultiplePerNode: true
  storage:
    useAllNodes: true
    useAllDevices: false
    config:
      databaseSizeMB: "1024"
      journalSizeMB: "1024"
EOF
```

You can use `kubectl` to check that the operator has created all the daemon pods corresponding to the cluster. The output should look something like this (there should be one OSD daemon and one completed prepare pod for each node):

```shell
$ kubectl --namespace=rook-ceph get pod
NAME                                                        READY     STATUS      RESTARTS   AGE
rook-ceph-mds-rook-shared-fs-a-6944db7659-qj6kc             1/1       Running     0          74m
rook-ceph-mds-rook-shared-fs-b-667494fd7-qnt7j              1/1       Running     0          73m
rook-ceph-mgr-a-6c79786fc6-kqj9q                            1/1       Running     0          3h43m
rook-ceph-mon-a-f4d9f7bd4-pps2z                             1/1       Running     0          3h44m
rook-ceph-mon-b-79b8d6b45d-qmn59                            1/1       Running     0          3h44m
rook-ceph-mon-d-7d45d85798-bdnqb                            1/1       Running     0          175m
rook-ceph-osd-0-86457f578b-pjpl6                            1/1       Running     0          3h42m
rook-ceph-osd-1-dbdc869bc-vfnpw                             1/1       Running     0          3h42m
rook-ceph-osd-2-6c54cc97b7-bcpzh                            1/1       Running     0          3h42m
rook-ceph-osd-prepare-rookmachines-869665964c-gsrfs-lmgnv   0/2       Completed   0          3h42m
rook-ceph-osd-prepare-rookmachines-869665964c-rfh7c-6t44k   0/2       Completed   0          3h42m
rook-ceph-osd-prepare-rookmachines-869665964c-xxlnx-mpblb   0/2       Completed   0          3h42m
rook-ceph-tools-575756777b-tlvts                            1/1       Running     0          42m
```

The cluster resource is also where you can affect the placement of the OSD pods on the nodes, e.g. in case you have some nodes that should not be used as ceph storage nodes. See the [Rook documentation](https://rook.io/docs/rook/v0.9/ceph-cluster-crd.html#placement-configuration-settings) for details.

### Deploy the Rook Shared File System

As soon as the rook-operator is functional, you can deploy the shared file system:

```shell
cat <<'EOF' | kubectl apply -f -
---
apiVersion: v1
kind: Namespace
metadata:
  name: rook-ceph
---
apiVersion: ceph.rook.io/v1
kind: CephFilesystem
metadata:
  name: rook-shared-fs
  namespace: rook-ceph
spec:
  metadataPool:
    replicated:
      size: 3
  dataPools:
  - replicated:
      size: 3
  metadataServer:
    activeCount: 1
    activeStandby: true
EOF
```

For more options, see the [Rook documentation](https://rook.github.io/docs/rook/v0.9/ceph-filesystem-crd.html) on creating shared file systems.

After some minutes the filesystem should be up and running:

```shell
$ kubectl -n rook-ceph get pod -l app=rook-ceph-mds
NAME                                              READY     STATUS    RESTARTS   AGE
rook-ceph-mds-rook-shared-fs-a-6944db7659-qj6kc   1/1       Running   0          84m
rook-ceph-mds-rook-shared-fs-b-667494fd7-qnt7j    1/1       Running   0          84m
```

### Deploy an Application with Rook

For easy cleanups we will create a new namespace for our tutorial:

```shell
$ kubectl create namespace read-write-many-tutorial
namespace/read-write-many-tutorial created
```

For the tutorial we will deploy a NGINX container with two replicas and a persistent volume named nginx-data:

```shell
cat <<'EOF' | kubectl apply --namespace=read-write-many-tutorial -f -
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
        flexVolume:
          driver: ceph.rook.io/rook
          fsType: ceph
          options:
            fsName: rook-shared-fs
            clusterNamespace: rook-ceph
      containers:
      - image: nginx:latest
        name: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: "/data"
          name: nginx-data
          readOnly: false
EOF
```

Wait until both pods are running:

```shell
$ kubectl get pods --namespace read-write-many-tutorial
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-84cfbc4d7c-nxbjq  1/1     Running   0          53s
nginx-deployment-84cfbc4d7c-rzk48  1/1     Running   0          53s
```

You can now store data into the volume of the first pod:

```shell
kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'echo "TEST" > /data/test.txt'
```

Now should be able to read this file from both pods:

```shell
$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST

$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[1].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST
```

### Cleanup Rook

Delete the namespace:

```shell
$ kubectl delete namespace read-write-many-tutorial
namespace "read-write-many-tutorial" deleted
```

If you also want to remove the Rook cluster run:

```shell
$ helm del --purge rook-operator
release "rook-operator" deleted

$ kubectl delete namespace rook-ceph-system
namespace "rook-ceph-system" deleted

$ kubectl delete namespace rook-ceph
namespace "rook-ceph" deleted
```

Additionally you have to remove some files from the nodes after deleting the cluster. Connect to each node and execute:

```shell
sudo rm -rf /var/lib/rook
```

### Further Information

* Besides a shared file system rook also supports object and block storage. Compare the [offical docs](https://github.com/rook/rook/tree/master/Documentation) for further information.
* The rook cluster requires quite some memory and cpu. On a 3-node setup with 300GB storage we observed about 2GiB RAM and about 200Mi CPU usage on the cluster. This may heavily depend on the usage of the cluster.

## NFS

This tutorial shows how you can enable RWX, by deploying a NFS server into your cluster that provides ReadWriteMany volumes.

!!! Note that the I/O performance of these NFS powered ReadWriteMany volumes will not be the greatest since reads and writes have to go through an additional NFS server. Also this NFS server is not highly available, the data in the underlying volume is replicated over the OpenStack cluster.

### Deploy the nfs-server-provisioner

You can deploy the [nfs-server-provisioner](https://github.com/helm/charts/tree/master/stable/nfs-server-provisioner) with Helm, which is automatically included in MetaKube clusters (See [Using Helm](../17.using-helm/default.en.md)):

```shell
helm install stable/nfs-server-provisioner --name nfs-provisioner --namespace nfs --set=persistence.enabled=true,persistence.storageClass=sys11-quobyte,persistence.size=5Gi
```

This will deploy a NFS server, that stores its data on a replicated ReadWriteOnce Quobyte volume. The NFS server is exposed through an additional StorageClass called `nfs`:

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

### Deploy an Application with NFS

After the NFS server and the new StorageClass are ready, you can deploy an application that uses this StorageClass to create a ReadWriteMany volume.

For easy cleanups we will create a new namespace for our tutorial:

```shell
$ kubectl create namespace read-write-many-tutorial
namespace/read-write-many-tutorial created
```

For the tutorial we will deploy a NGINX container with two replicas and a persistent volume named nginx-data:

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

You can now store data into the volume of the first pod:

```shell
kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'echo "TEST" > /data/test.txt'
```

Now should be able to read this file from both pods:

```shell
$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[0].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST

$ kubectl exec --namespace read-write-many-tutorial $(kubectl get pods --namespace read-write-many-tutorial -o jsonpath='{.items[1].metadata.name}') -i -t -- bash -c 'cat /data/test.txt'
TEST
```

### Cleanup NFS

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

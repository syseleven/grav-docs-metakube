---
title: 'Create a backup and restore from it'
published: true
taxonomy:
    tag:
        - backup
        - kubernetes
        - cli
        - ark
        - velero
---

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).
* You need to have [installed the Velero Backups Add-On](../../03.addons/02.metakube-backups/default.en.md)
* You need to have the Velero CLI installed, which can be downloaded for your OS from [GitHub](https://github.com/heptio/velero/releases).
* If you use macOS you may install the Velero client with home brew.
* For inspection of the S3 Bucket where the backups are stored, install and configure [S3cmd](https://s3tools.org/s3cmd) as described in the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/en/reference/object-storage). The region is either `s3.cbk.cloud.syseleven.net` or `s3.dbl.cloud.syseleven.net`. Your credentials can be retrieved with `openstack ec2 credentials list`.

## Deploy an application

For easy cleanups we create a new namespace for our tutorial:

```shell
$ kubectl create namespace backup-tutorial
namespace/backup-tutorial created
```

For our tutorial we will deploy an NGINX container with a persistent volume named nginx-logs. It is important to note the annotation which is added to the pod spec. Without the annotation your volume will not be included in the backup task. The annotation must include the names of the volumes to backup. This can be a single volume or a comma separated list of volumes. The backup task is performed with the [Velero Restic](https://heptio.github.io/velero/master/restic.html) integration.
Note: [hostPath](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath) volumes are not supported, but the new [local volume](https://kubernetes.io/docs/concepts/storage/volumes/#local) type is supported.

```shell
cat <<'EOF' | kubectl --namespace=backup-tutorial apply -f -
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backup
  template:
    metadata:
      labels:
        app: backup
      annotations:
        backup.velero.io/backup-volumes: nginx-logs
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
    app: backup
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

Check that the NGINX pod has been created successfully and is running:

```shell
$ kubectl get pods --namespace backup-tutorial
NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-c7df76c4f-bq2zj   1/1       Running   0          1m
```

You can also check that the persistent volume claim has been created:

```shell
$ kubectl get pvc --namespace backup-tutorial
NAME         STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
nginx-logs   Bound     pvc-4cad1837-bc17-11e8-b699-0a580af40873   1Gi        RWO            sys11-quobyte   3m
```

## Create a backup of your application

We can now create a backup of the complete tutorial namespace:

```shell
$ velero backup create backup-tutorial --include-namespaces backup-tutorial
Backup request "backup-tutorial" submitted successfully.
Run `velero backup describe backup-tutorial` for more details.
```

With `velero backup get` you will also get a list of all available backups:

```shell
$ velero backup get
NAME                    STATUS      CREATED                          EXPIRES   SELECTOR
backup-tutorial         Completed   2018-09-19 16:32:32 +0200 CEST   29d       <none>
```

## Delete the application and restore the application state from the backup

To delete the application we can just delete our complete tutorial namespace:

```shell
$ kubectl delete namespace backup-tutorial
namespace "backup-tutorial" deleted
```

Now we can use the backup to restore the previous state of our deployment:

```shell
$ velero restore create --from-backup backup-tutorial
Restore request "backup-tutorial-20180919163439" submitted successfully.
Run `velero restore describe backup-tutorial-20180919163439` for more details.
```

Velero will now restore the namespace the deployment and the pods running in it:

```shell
$ kubectl -n backup-tutorial get po
NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-c7df76c4f-bq2zj   1/1       Running   0          29s
```

Velero will also make sure that your persistent volume including the content is restored if you add the correct annotation to the pod spec:

```shell
$ kubectl -n backup-tutorial get pvc
NAME         STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
nginx-logs   Bound     pvc-24afa31c-bc19-11e8-b699-0a580af40873   1Gi        RWO            sys11-quobyte   1m
```

## Remove the backup backup-turorial

Delete the backup:

```shell
$ velero backup delete backup-tutorial
Are you sure you want to continue (Y/N)? Y
Request to delete backup "backup-tutorial" submitted successfully.
The backup will be fully deleted after all associated data (disk snapshots, backup files, restores) are removed.
```

## Create a scheduled backup

In this example we will create a scheduled backup that runs every 30 minutes. We also set the backup TTL to 48 hours. If you omit the TTL the oldest backup will be removed after 30 days.

```shell
$ velero create schedule backup-tutorial --schedule="*/30 * * * *" --include-namespaces backup-tutorial --ttl 48h0m0s
Schedule "backup-tutorial" created successfully.
```

You can view the scheduled backup with Velero:

```shell
$ velero schedule get
NAME              STATUS    CREATED                          SCHEDULE       BACKUP TTL   LAST BACKUP   SELECTOR
backup-tutorial   Enabled   2018-09-19 16:53:38 +0200 CEST   */30 * * * *   48h0m0s      5m ago        <none>
```

## Inspect the S3 Bucket

If you have installed and configured S3cmd, you can use it to list the backup files Velero created in your object storage. You should at least see the folders `backups`, `restic` and `metadata` in your S3bucket. Depending on the usage you will also find a `restores` folder. Restic is responsible for the volume backups:

```shell
$ s3cmd la --recursive

2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/backups/
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/backups/backup-tutorial/
2018-09-19 14:32       926   s3://metakube-cluster-backup-rjxcqg4986/backups/backup-tutorial/velero-backup.json
2018-09-19 14:32      2438   s3://metakube-cluster-backup-rjxcqg4986/backups/backup-tutorial/backup-tutorial-logs.gz
2018-09-19 14:32      5705   s3://metakube-cluster-backup-rjxcqg4986/backups/backup-tutorial/backup-tutorial.tar.gz

2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/metadata/
2018-09-19 14:32        36   s3://metakube-cluster-backup-rjxcqg4986/metadata/revision

2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/
2018-09-19 14:32       155   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/config
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/data/
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/data/d5/
2018-09-19 14:32      1435   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/data/d5/d5dec01c5cdab25b95338fc1c168068c40ff0e43128878524b8e883ea4d595e9
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/index/
2018-09-19 14:32       353   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/index/688ce096949c6a21c56790989308fe651ab723080b0ba7349480f8e2421e7048
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/keys/
2018-09-19 14:32       453   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/keys/e92c806e8f15ae460990ade4b1625d0f8e6fb9edec57d7d683e5822e822d669e
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/locks/
2018-09-19 14:32         0   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/snapshots/
2018-09-19 14:32       505   s3://metakube-cluster-backup-rjxcqg4986/restic/backup-tutorial/snapshots/e976bddd8cede60046fb00780946fc32becdfa1f7bccc029de73de5fea1ac083
```

## Clean up

Delete the namespace:

```shell
$ kubectl delete namespace backup-tutorial
namespace "backup-tutorial" deleted
```

## Patch an existing deployment with kubectl

Patch an existing deployment with kubectl to add the annotation to include volume backups:

```shell
kubectl -n backup-tutorial patch deployment nginx-deployment -p '{"spec":{"template":{"metadata":{"annotations":{"backup.velero.io/backup-volumes": "nginx-logs"}}}}}'
```

**We recommend creating a separate backup job for each namespace you would like to backup. Otherwise you might have problems trying to restore individual objects across namespaces.**

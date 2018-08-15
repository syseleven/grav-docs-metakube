---
title: 'Create a backup and restore from it'
published: false
taxonomy:
    tag:
        - backup
        - kubernetes
        - cli
---

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).
* You need to have the Ark CLI installed, which can be download for your OS from [GitHub](https://github.com/heptio/ark/releases).
* If you use Mac OSX you may install the ark client with home brew.
* For inspection of the S3 Bucket where the backups are stored, install and configure [S3cmd](https://s3tools.org/s3cmd) as described in the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/en/documentation/object-storage).

## Deploy an application

For easy cleanups we create a new namespace for our tutorial

```shell
$ kubectl create namespace backup-tutorial
namespace/backup-tutorial created
```

For our tutorial we will just deploy a NGINX Hello World app, run:

```shell
$ kubectl run hello-app --image=nginxdemos/hello --port=80 --replicas=2 --namespace backup-tutorial
deployment.apps/hello-app created
```

Check that the pods of the new application were created successfully and are running:

```shell
$ kubectl get pods --namespace backup-tutorial
NAME                           READY     STATUS    RESTARTS   AGE
hello-app-5c7477d7b7-n44wq     1/1       Running   0          9s
hello-app-5c7477d7b7-sv5sw     1/1       Running   0          9s
```

## Create a backup of your application

We can now create a backup of the complete tutorial namespace:

```shell
$ ark backup create hello-app-backup-1 --include-namespaces backup-tutorial --include-resources "*"
Backup request "hello-app-backup-1" submitted successfully.
Run `ark backup describe hello-app-backup-1` for more details.
```

With `ark backup get` you will also get a list of all available backups:

```shell
$ ark backup get
NAME                                   STATUS      CREATED                          EXPIRES   SELECTOR
hello-app-backup-2018-08-13-14-46-18   Completed   2018-08-13 14:46:19 +0200 CEST   29d       <none>
```

## Delete the application and restore the application state from the backup

To delete the application we can just delete our complete tutorial namespace:

```shell
$ kubectl delete namespace backup-tutorial
namespace "backup-tutorial" deleted
```

Now we can use the backup to restore the previous state of our application:

```shell
$ ark restore create --from-backup hello-app-backup-1
Restore request "hello-app-backup-1-20180813145116" submitted successfully.
Run `ark restore describe hello-app-backup-1-20180813145116` for more details.
```

Ark will now restore the namespace the deployment and the pods running in it:

```shel
$ kubectl get pods
NAME                         READY     STATUS    RESTARTS   AGE
hello-app-5c7477d7b7-2qpmm   1/1       Running   0          6s
hello-app-5c7477d7b7-64psb   1/1       Running   0          6s
```

## Inspect the S3 Bucket

If you have installed and configured S3cmd, you can use it to list the backup files ark created in your object storage:

```shel
$ s3cmd la --recursive


2018-08-07 15:00         0   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/
2018-08-07 15:00       953   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/ark-backup.json
2018-08-07 15:00      2145   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/hello-app-backup-1-logs.gz
2018-08-07 15:00      2837   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/hello-app-backup-1.tar.gz
2018-08-07 15:00       649   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/restore-hello-app-backup-1-20180813145116-logs.gz
2018-08-07 15:00        82   s3://metakube-cluster-backup-fd4jtkc2p9-ark/hello-app-backup-1/restore-hello-app-backup-1-20180813145116-results.gz
```

## Clean up

Delete the namespace:

```shell
$ kubectl delete namespace backup-tutorial
namespace "backup-tutorial" deleted
```

Delete the backup (due to a bug in ark this has to be done **twice to actually delete the backup):

```shell
$ ark backup delete hello-app-backup-1
Are you sure you want to continue (Y/N)? y
Request to delete backup "hello-app-backup-1" submitted successfully.
The backup will be fully deleted after all associated data (disk snapshots, backup files, restores) are removed.
```

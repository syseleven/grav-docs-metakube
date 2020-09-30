---
title: 'Sharing Volumes between Pods'
published: true
taxonomy:
    tag:
        - kubernetes
        - cronjob
        - persistent-storage
        - volumes
        - readwritemany
---

## Overview

This document describes how to setup a demo deployment with a [persistent volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Normally, when using a ReadWriteOnce Volume, you cannot mount this volume onto another pod.
This is normally only possible with RWX (ReadWriteMany), which is currently not supported by MetaKube. For some use cases the following can be a valuable workaround to achive similar results.

When you spawn a pod, other pods on the same compute node may mount this volume too. This can be achieved by using [Kubernetes Pod Affinities](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity). In this example we are going to use this method to spawn a deployment with a volume and we will spawn multiple cronjob pods that can access this volume.

!! Keep in mind that this is no real subsitute for RWX. All your pods will be spawned on the same compute node.

## Create a deployment, a PVC and a cronjob

### deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-client
spec:
  strategy:
    type: Recreate
  replicas: 1
  selector:
    matchLabels:
      app: test-client
  template:
    metadata:
      labels:
        app: test-client
    spec:
      containers:
      - name: test-client
        image: ubuntu
        imagePullPolicy: Always
        command: ["sleep"]
        args:
        - "9999999"
        volumeMounts:
        - mountPath: "/mnt"
          name: data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: test-client
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-client
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: test-client-cron
spec:
  concurrencyPolicy: Forbid
  failedJobsHistoryLimit: 1
  jobTemplate:
    spec:
      template:
        spec:
          affinity:
            podAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                - labelSelector:
                    matchLabels:
                      app: test-client
                  topologyKey: "kubernetes.io/hostname"
          restartPolicy: OnFailure
          containers:
          - args:
            - "999999"
            command: ["sleep"]
            image: ubuntu
            name: test-client-cron
            volumeMounts:
            - mountPath: "/mnt"
              name: data
          volumes:
          - name: data
            persistentVolumeClaim:
              claimName: test-client
  schedule: 10 * * * *
```

### Deployment and creation of jobs

```shell
$ kubectl apply -f deployment.yaml
deployment.apps/test-client created
persistentvolumeclaim/test-client created
cronjob.batch/test-client-cron created
$ kubectl create job --from=cronjob/test-client-cron test1
job.batch/test1 created
$ kubectl create job --from=cronjob/test-client-cron test2
job.batch/test2 created
```

### Verification

We will create a test file on the deployment pod and see that we can access and modifiy data on the same volume at the same time.

(Your pod names will be different because the suffix is automatically generated.)

You can see that your pods are all being spawned on the same compute node:

```shell
$ kubectl get pod,job -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP            NODE                                     NOMINATED NODE   READINESS GATES
pod/test-client-859c9f78dc-5tddj   1/1     Running   0          40s   172.25.1.50   metakube-worker-vwkkx-57d85bfc67-s8l5b   <none>           <none>
pod/test1-cfd4v                    1/1     Running   0          19s   172.25.1.54   metakube-worker-vwkkx-57d85bfc67-s8l5b   <none>           <none>
pod/test2-846w5                    1/1     Running   0          12s   172.25.1.55   metakube-worker-vwkkx-57d85bfc67-s8l5b   <none>           <none>

NAME              COMPLETIONS   DURATION   AGE   CONTAINERS         IMAGES         SELECTOR
job.batch/test1   0/1           19s        19s   test-client-cron   ubuntu         controller-uid=ce570829-5066-4a53-8de0-4f272190b667
job.batch/test2   0/1           12s        12s   test-client-cron   ubuntu         controller-uid=80bd4789-2314-4ff0-8a50-b568e060858b
```

#### Create a test file on deployment pod

```shell
$ kubectl exec -it test-client-859c9f78dc-5tddj -- touch /mnt/ThisIsNew
```

#### Check if test1-job pod can see the test file

```shell
$ kubectl exec -it test1-cfd4v -- ls -la /mnt/ThisIsNew
-rw-r--r-- 1 root root 0 Jun 24 13:14 /mnt/ThisIsNew
```

#### Delete the test file via test2-job pod

```shell
$ kubectl exec -it test2-846w5 -- rm /mnt/ThisIsNew
```

#### See if the test file is gone from deployment pod

```shell
$ kubectl exec -it test-client-859c9f78dc-5tddj -- ls -la /mnt/ThisIsNew
ls: cannot access '/mnt/ThisIsNew': No such file or directory
```

## Clean up

Delete all created resources.

```shell
$ kubectl delete -f deployment.yaml
kubectl get deployment.apps "test-client" deleted
persistentvolumeclaim "test-client" deleted
cronjob.batch "test-client-cron" deleted
```

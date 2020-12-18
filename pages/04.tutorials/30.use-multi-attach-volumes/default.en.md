---
title: 'How to use multi attach volumes'
published: true
date: '17-12-2020 15:00'
taxonomy:
    tag:
        - metakube
        - cluster
        - rwx
        - storage
        - volumes
        - multi attach
---

## Using Multi Attach Volumes (Read Write Many)

To use multi attach volumes or read write many volumes you need to use a specific storage class in your metakube cluster. We currently offer two preinstalled storage classes.


``` bash
kubectl get sc
NAME                      PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE
sys11-quobyte (default)   cinder.csi.openstack.org   Delete          WaitForFirstConsumer
sys11-quobyte-multi       cinder.csi.openstack.org   Delete          Immediate
```

The default storage class provides regular block storage that can only be attached to a single worker node. The storage class sys11-quobyte-multi privides a multi attach storage class. You may use volumes created with this storage class to attach to multiple worker nodes and pods. Please do not use this storage class as a substitute for a replicated cluster. The volumes created with this storage class use a regular ext4 file system. They do not support I/O fencing. I/O fencing protocols control write access in a shared storage environment to maintain data consistency. Your applications must provide write ordering for the attached instances to maintain data consistency.
One of the current limitations of using multi attch volumes are the file system permissions. The volumes are create and bound as the root user. Which causes problems running non root containers. As you will not be able to read or write any files on the volumes. This small problem can be solved with an init container. To do this you must insure that your init container can access your volumes:

``` yaml
spec:
  initContainers:
   - name: volume-permissions
     image: busybox
     command: ['sh', '-c']
     args: ['chown -R 101:101 /usr/local/tmp]
     volumeMounts:
     - name: webapp-pv-tmp
       mountPath: usr/local/tmp
     - name: webapp-pv-test
       mountPath: usr/local/test
  containers:
  - name: hello
    image: syseleven/metakube-hello:1.0.0
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: webapp-pv-tmp
      mountPath: usr/local/tmp
    - name: webapp-pv-test
      mountPath: usr/local/test
  volumes:
  - name: webapp-pv-tmp
    persistentVolumeClaim:
    claimName: webapp-pv-tmp
  - name: webapp-pv-test
    persistentVolumeClaim:
    claimName: webapp-pv-test
```

In this case the pod metakube-hello is running as the user nginx or the uid/gid 101. This is specified in the Dockerfile. You can check that you are using the correct storage class and access mode. All you need to do is to query your PVC:

``` bash
 kubectl get pvc
NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS          AGE
webapp-pv-test   Bound    pvc-aab781b8-05b7-44bc-9fda-048adc25792f   1Gi        RWX            sys11-quobyte-multi   21m
webapp-pv-tmp    Bound    pvc-88c3625a-34ba-48ea-810a-d972ded0b7fe   1Gi        RWX            sys11-quobyte-multi   21m
```

Here you can see that both volumes are mounted and are using the access mode RWX (read write many).




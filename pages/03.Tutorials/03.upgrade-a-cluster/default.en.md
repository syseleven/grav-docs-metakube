---
title: 'Upgrade a cluster'
media_order: 'metakube_upgrade-cluster_01.png,metakube_upgrade-cluster_02.png'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - gui
        - cluster
---

## Upgrade master components

When an upgrade for the master nodes is available, a blue text `upgrade available` will be shown besides the Master version

![](metakube_upgrade-cluster_01.png)

To start the upgrade, just click on the link and choose the desired version (most recent, tested version is selected)

![](metakube_upgrade-cluster_02.png)

When initiated the master components will be upgraded in the background. All newly created worker nodes will be installed with the new version, but existing nodes will not be changed. Refer to the next chapter on how to upgrade your existing worker nodes.

## Upgrade worker nodes

The worker nodes can not be upgraded in place. This means they have to be replaced by newer versions to be upgraded. The basic upgrade path is

1. Create new worker nodes
2. Disable scheduling on the old worker nodes
3. Drain one old worker node
4. Delete drained worker node
5. go to 3 (or 1, if you did not create equally many new worker nodes) until all old worker nodes are deleted

### Create new worker nodes

Create a set of new worker nodes. For detailed information on how to do this, refer to [Add a worker node](/tutorials/add-a-worker-node)

### Disable scheduling on the old worker nodes

You can disable scheduling onto a worker node with `kubectl cordon ${node}`. Example:

```bash
$ kubectl cordon kubermatic-w9tk8cmw62-hm5vl
node "kubermatic-w9tk8cmw62-hm5vl" cordoned

$ kubectl get nodes
NAME                          STATUS                     ROLES     AGE       VERSION
kubermatic-w9tk8cmw62-22wgv   Ready,SchedulingDisabled   <none>    6h        v1.9.6
kubermatic-w9tk8cmw62-58pd6   Ready,SchedulingDisabled   <none>    6h        v1.9.6
kubermatic-w9tk8cmw62-7q9nx   Ready                      <none>    1m        v1.10.2
kubermatic-w9tk8cmw62-hm5vl   Ready,SchedulingDisabled   <none>    6h        v1.9.6
kubermatic-w9tk8cmw62-slh7v   Ready                      <none>    1m        v1.10.2
```

Before you start draining old nodes, you should disable scheduling on all old nodes, to reduce rescheduling overhead. If you do otherwise, a pod could be rescheduled from A to B, and on drain of B to C, etc going through the whole (old) cluster.

### Drain one old worker node

When all old nodes are disabled for scheduling, you can start to drain these nodes. This means, all pods are evicted from the node. As daemonsets are scheduled on all available nodes, you must ignore them with `--ignore-daemonsets` and local storage must be deleted with `--delete-local-data`.

```bash
$ kubectl drain --delete-local-data --ignore-daemonsets kubermatic-w9tk8cmw62-22wgv
node "kubermatic-w9tk8cmw62-22wgv" already cordoned
WARNING: Ignoring DaemonSet-managed pods: canal-hgrlh, kube-proxy-s5tnt, npd-v0.4.1-g5wqj
pod "traefik-ingress-controller-57b4767f99-8rrgg" evicted
node "kubermatic-w9tk8cmw62-22wgv" drained
```

### Delete drained worker node

When the worker node is drained, you can safely delete them. For detailed information on how to do this, refer to [Delete a worker node](/tutorials/delete-a-worker-node). If you had enough free resources to create a complete set of new worker nodes, or have created enough to hold the current load, you can continue to drain the next old node ('go to 3').
Otherwise you should create more new workernodes now ('go to 1').
When everything is done, you should have a new set of worker nodes with the new version

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES     AGE       VERSION
kubermatic-w9tk8cmw62-7q9nx   Ready     <none>    10m       v1.10.2
kubermatic-w9tk8cmw62-q2zr4   Ready     <none>    4m        v1.10.2
kubermatic-w9tk8cmw62-slh7v   Ready     <none>    10m       v1.10.2
```

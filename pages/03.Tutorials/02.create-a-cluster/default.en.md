---
title: 'Create a cluster'
media_order: 'metakube_create-cluster_00.png,metakube_create-cluster_01.png,metakube_create-cluster_02.png,metakube_create-cluster_03.png,metakube_create-cluster_06.png,metakube_create-cluster_08.png,metakube_create-cluster_09.png,metakube_create-cluster_10.png,metakube_create-cluster_07.png,metakube_create-cluster_05.png,metakube_create-cluster_06.png,metakube_create-cluster_11.png,metakube_create-cluster_12.png'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - gui
        - cluster
---

## Place to live

To create a new cluster open the MetaKube dashboard and select the menu entry `Create Cluster`.
Start by choosing a name

![](metakube_create-cluster_00.png)
![](metakube_create-cluster_01.png)

the provider (on writing this document only SysEleven Stack is supported)

![](metakube_create-cluster_02.png)

and the region, where the cluster shall live

![](metakube_create-cluster_03.png)

## Create the cluster

In the next step of the installer, enter the domain-name and your SysEleven Stack credentials into the `Provide credentials` tab

![](metakube_create-cluster_06.png)

Then choose the tenant where you want to create the cluster in in the drop-down

![](metakube_create-cluster_07.png)

As node settings, we recommend at least 3 nodes sized `m1.small` or bigger

![](metakube_create-cluster_08.png)

You can also choose the operating system you want to use for your worker nodes

![](metakube_create-cluster_09.png)

A default image tested and maintained by SysEleven is automatically chosen for the nodes, but you can also enter any image available to you in SysEleven Stack. To get a list of current base images, navigate to `Project -> Compute -> Images` in the SysEleven Stack dashbaord

![](metakube_create-cluster_05.png)

Make sure to enter the exact name of the image you want to run on your machines, as listed in the images tab of the SysEleven stack dashboard.  
The chosen SSH key will be used for authentication as user `apiserver` on all worker nodes.

![](metakube_create-cluster_10.png)

When you click on next, you will see a summary and the cluster creation will start if you confirm. You will be forwarded to the creation overview on confirmation, to see your new cluster being deployed

![](metakube_create-cluster_11.png)
![](metakube_create-cluster_12.png)

After all master components are ready, your cluster will create the configured amount of worker nodes in your SysEleven Stack tenant. Fully created nodes will be marked with a green dot, pending ones with a yellow circle. You should [download the kubeconfig](/tutorials/download-the-kubeconfig) now, to be able to use `kubectl` with your cluster.
After all nodes are created you can use `kubectl` to view and check the status of the created nodes

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES     AGE       VERSION
kubermatic-w9tk8cmw62-22wgv   Ready     <none>    1m        v1.9.6
kubermatic-w9tk8cmw62-58pd6   Ready     <none>    1m        v1.9.6
kubermatic-w9tk8cmw62-hm5vl   Ready     <none>    1m        v1.9.6
```

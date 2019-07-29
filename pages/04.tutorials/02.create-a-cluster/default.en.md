---
title: 'Create a cluster'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - gui
        - metakube
        - cluster
---

## Place to live

To create a new cluster, select a MetaKube Project from login page and click the button entry `Add Cluster`. Clusters are tied to a [project](../../02.documentation/02.projects/default.en.md). You can create as many clusters within one project as you want.
Start by choosing a name:

![Overview of cluster creation](image_overview_01.png)
![Overview of cluster creation with filled out name](image_overview_02.png)

the [provider](../../02.documentation/05.supported-cloud-providers/default.en.md):

![Menu to chose cloud provider](image_provider_01.png)

and the [region](../../02.documentation/05.supported-cloud-providers/default.en.md) where the cluster shall live:

![Menu to choose datacenter](image_datacenter_01.png)

## Create the cluster

In the next step of the installer, enter the domain name and your SysEleven Stack credentials into the `Provide credentials` tab:

![Overview of cluster settings](image_cluster-settings_01.png)

Then choose the tenant where you want to create the cluster from the drop-down list:

![Overview of cluster settings with highlighted tenant section](image_cluster-settings_02.png)

For node settings we recommend at least 3 nodes sized `m1.small` or bigger. For an overview of the possible node flavors see the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/en/reference/compute#flavors). It is also possible to choose the operating system and image used for your worker nodes:

![Overview of cluster settings with highlighted node section](image_cluster-settings_03.png)


A default image tested and maintained by SysEleven is automatically chosen for the nodes, but you can also enter any image available to you in SysEleven Stack. To get a list of current base images, navigate to `Project -> Compute -> Images` in the SysEleven Stack dashboard:

![Overview of openstack images](image_stack-images_01.png)

Make sure to enter the exact name of the image you want to run on your machines, as listed in the images tab of the SysEleven stack dashboard. See also [supported operating systems](../../02.documentation/04.supported-operating-systems/default.en.md).
The chosen SSH key will be used for authentication for the default user (e.g. `ubuntu` for Ubuntu images) on all worker nodes. When you click on next, you will see a summary and the cluster creation will start after you confirm. You will then be forwarded to the cluster creation page where you can view the cluster creation process:

![Cluster summary before creation](image_cluster_summary_01.png)
![Cluster details in creation state](image_cluster-details_01.png)

After all of the master components are ready, your cluster will create the configured number of worker nodes in your SysEleven Stack tenant. Fully created nodes will be marked with a green dot, pending ones with a yellow circle. You may [download the kubeconfig](../06.download-the-kubeconfig/default.en.md) now to be able to use `kubectl` with your cluster or just use the provided [Web Terminal](../../02.documentation/11.metakube-webterminal/default.en.md). After all nodes are created you can use `kubectl` to view and check the status of the created nodes:

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES     AGE       VERSION
kubermatic-w9tk8cmw62-22wgv   Ready     <none>    1m        v1.9.6
kubermatic-w9tk8cmw62-58pd6   Ready     <none>    1m        v1.9.6
kubermatic-w9tk8cmw62-hm5vl   Ready     <none>    1m        v1.9.6
```

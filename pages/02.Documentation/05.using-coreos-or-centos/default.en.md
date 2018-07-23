---
title: 'Using CoreOS or CentOS'
published: false
date: '23-07-2018 16:26'
taxonomy:
    tag:
        - metakube
        - cluster
        - node
---

The default operating system (OS) for MetaKube clusters is Ubuntu, and we provide default images for the latest tested release. We also support clusters running CentOS or CoreOS Container Linux, but as of writing this document, we do not provide images for either of these distributions.

To run a cluster with one of them, you can use the official [CoreOS](https://coreos.com/os/docs/latest/booting-on-openstack.html) or [CentOS](http://cloud.centos.org/centos/7/images/) cloud images. Follow the guideline on [how to use a custom image](../tutorials/how-to-use-custom-images-for-your-worker-nodes) and use the qcow2 image of your choice.
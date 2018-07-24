---
title: 'Supported Operating Systems'
published: true
date: '23-07-2018 16:26'
taxonomy:
    tag:
        - metakube
        - cluster
---

## Default

The default operating system for MetaKube clusters is **Ubuntu**, and we provide images for the latest tested release.

## Supported

Additionaly to Ubuntu we support clusters running **CentOS** or **CoreOS Container Linux**, but as of writing this document, we do not provide images for either of these distributions.

To run a cluster with one of them, you can use the official [CoreOS](https://coreos.com/os/docs/latest/booting-on-openstack.html) or [CentOS](http://cloud.centos.org/centos/7/images/) cloud images. Follow the guideline on [how to use a custom image](../../03.Tutorials/12.how-to-use-custom-images-for-your-worker-nodes/default.en.md) and use the qcow2 image of your choice.

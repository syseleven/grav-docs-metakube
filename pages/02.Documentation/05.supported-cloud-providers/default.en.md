---
title: 'Supported Cloud Providers'
taxonomy:
    tag:
        - metakube
        - cluster
---

MetaKube currently supports the following cloud providers and regions for creating Kubernetes clusters:

## SysEleven OpenStack

Supported regions:

* cbk
* dbl

Note that every region has its own set of resource quotas.

When creating a cluster you have to provide valid SysEleven OpenStack credentials for the OpenStack tenant that you want to create the cluster in, so that MetaKube can create and manage VMs, Networks, LoadBalancers and Volumes.

For more information see the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/).

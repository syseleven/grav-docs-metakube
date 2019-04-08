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

Note that every region has its own set of resource quotas. You can see the quota for every region in the [OpenStack Dashboard](https://dashboard.cloud.syseleven.net).

If you want to increase the quota for a region, please contact our [Support](../../04.Support/default.en.md).

When creating a cluster you have to provide valid SysEleven OpenStack credentials for the OpenStack tenant that you want to create the cluster in, so that MetaKube can create and manage VMs, Networks, LoadBalancers and Volumes.

For more information see the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/).

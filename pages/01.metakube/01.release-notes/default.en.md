---
title: 'Release Notes'
taxonomy:
    tag:
        - metakube
        - release-notes
---
## 2018-09-13

* Kubernetes versions 1.10.7 and 1.11.3 are now available


## 2018-09-05

* The `default` namespace now comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
  that assigns a default CPU and Memory Request to pods that do not define one explicitly.

## 2018-08-22

* Kubernetes version 1.11.1 and 1.11.2 are now available
* Clusters now come with managed Cross-Region Cluster Backup Solution, see [Backups](../../02.Documentation/06.backups/default.en.md)
* Security Groups in [LoadBalancer Services](../../03.Tutorials/13.create-a-load-balancer/default.en.md) are managed automatically
* Clusters now come with managed metrics-server which enables [Horizontal Pod AutoScaling](../../03.Tutorials/19.use-horizontal-pod-autoscaling/default.en.md)
* Cluster Creation: Drop-Downs instead of free-text input boxes to choose from available Floating IP Pools, Security Groups, Networks and Subnets

## 2018-08-08

* Kubernetes versions 1.10.6 and 1.9.10 are now available

## 2018-08-01

* MetaKube is now generally available

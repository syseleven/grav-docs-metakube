---
title: 'Release Notes'
taxonomy:
    tag:
        - metakube
        - release-notes
page-toc:
    active: false
---

## 2019-02-15

* The dashboard shows now the Kernel and Container Runtime Versions of nodes, which makes it easier to see if a node is affected by a security vulnerability

## 2019-02-14

* Kubernetes versions 1.10.x are not available anymore corresponding to our [Supported Versions Matrix](../../02.Documentation/03.supported-kubernetes-versions/default.en.md)
* You can now add [tags](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) to nodes when adding them
* For new clusters and new nodes in existing clusters, nodes are now wrapped inside of NodeDeployments which makes it possible to trigger rolling updates of nodes within a cluster from the dashboard
* New nodes now receive Docker 18.9.2 which fixes [CVE-2019-5736 in Runc](https://kubernetes.io/blog/2019/02/11/runc-and-cve-2019-5736/) for all new nodes
* Multiple owners can now be added to one MetaKube [project](../../02.Documentation/02.projects/default.en.md)
* Detailed events for the lifecycle of a node are shown in the dashboard

## 2019-02-13

* Every MetaKube cluster comes now with a [web-terminal](../../02.Documentation/10.metakube-webterminal/default.en.md) that is accessible from the [MetaKube dashboard](../../02.Documentation/09.metakube-dashboard/default.en.md)

## 2019-02-07

* Kubernetes version 1.13.3 is now available

## 2019-01-23

* Updated [Helm](https://www.helm.sh/) to [2.12.3](https://github.com/helm/helm/releases/tag/v2.12.3)

## 2019-01-22

* Kubernetes version 1.12.5 is now available
* The master service health indicators on the cluster details page now contains all services that are fully managed

## 2019-01-17

* Kubernetes versions 1.13.1 and 1.13.2 are now available

## 2019-01-15

* Updated [Helm](https://www.helm.sh/) to [2.12.2](https://github.com/helm/helm/releases/tag/v2.12.2)
* The cluster details page now comes with [a dashboard](../../02.Documentation/09.metakube-dashboard/default.en.md) showing the amount of pods in the cluster and the cpu and memory usage of the worker nodes

## 2018-12-20

* Kubernetes versions 1.10.12 and 1.12.4 are now available
* Updated [Helm](https://www.helm.sh/) to [2.12.1](https://github.com/helm/helm/releases/tag/v2.12.1)
* Updated [kubernetes-dashboard](../../02.Documentation/05.kubernetes-dashboard/default.en.md) to [1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1)

## 2018-12-04

* Disabled all Kubernetes versions before 1.10.11 and 1.12.3 due to a [Kubernetes security incident](https://github.com/kubernetes/kubernetes/issues/71411)

## 2018-11-26

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.10.0](https://github.com/heptio/ark/releases/tag/v0.10.0)
* Updated [metrics-server](https://github.com/kubernetes-incubator/metrics-server) to [0.3.1](https://github.com/kubernetes-incubator/metrics-server/releases/tag/v0.3.1)
* Kubernetes versions 1.10.11 and 1.12.3 are now available

## 2018-11-19

* MetaKube now supports [horizontal node autoscaling](../../03.Tutorials/20.use-horizontal-node-autoscaling/default.en.md)
* Kubernetes version 1.10.10 is now available

## 2018-11-13

* Allow resizing of persistent volumes. See [Resizing persistent volumes](../../03.Tutorials/21.resize-persistent-volume/default.en.md).

## 2018-11-09

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.11](https://github.com/heptio/ark/releases/tag/v0.9.11)

## 2018-11-02

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.10](https://github.com/heptio/ark/releases/tag/v0.9.10)

## 2018-10-31

* MetaKube now supports [projects](../../02.Documentation/02.projects/default.en.md) to group, maintain and access clusters with multiple users
* Kubernetes versions 1.10.9, 1.12.1 and 1.12.2 are now available
* etcd is updated to 3.3.9
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.9](https://github.com/heptio/ark/releases/tag/v0.9.9)
* New Ubuntu worker nodes are now using Ubuntu 18.04
* Kubernetes versions 1.11.* are now disabled due to a [bug in kubernetes](https://github.com/kubernetes/kubernetes/issues/68270) where nodes will loose their IP addresses

## 2018-10-08

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.7](https://github.com/heptio/ark/releases/tag/v0.9.7)

## 2018-10-04

* Kubernetes versions 1.9.11 and 1.10.8 are now available
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.6](https://github.com/heptio/ark/releases/tag/v0.9.6)

## 2018-09-27

* Clusters now come with [Helm](https://www.helm.sh/) already installed and configured. See also [Using Helm](../../03.Tutorials/17.using-helm/default.en.md).

## 2018-09-18

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.5](https://github.com/heptio/ark/releases/tag/v0.9.5) which fixes the restic integration ([Release Notes](https://github.com/heptio/ark/releases/tag/v0.9.5))

## 2018-09-14

* MetaKube is now a [Certified Hosted Kubernetes Platform](https://landscape.cncf.io/landscape=certified-kubernetes-hosted&selected=sys-eleven-meta-kube). The conformance test results are publicly available for Kubernetes [1.10](https://github.com/cncf/k8s-conformance/tree/master/v1.10/metakube) and [1.11](https://github.com/cncf/k8s-conformance/tree/master/v1.11/metakube)

## 2018-09-13

* Kubernetes versions 1.10.7 and 1.11.3 are now available

## 2018-09-12

* [kubernetes-dashboard](../../02.Documentation/05.kubernetes-dashboard/default.en.md) is updated to version [1.10.0](https://github.com/kubernetes/dashboard/releases/tag/v1.10.0)

## 2018-09-05

* The `default` namespace now comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
  that assigns a default CPU and memory request to pods that do not define one explicitly. See also [LimitRange](../../02.Documentation/08.kubernetes-limitrange/default.en.md).

## 2018-08-22

* Kubernetes version 1.11.1 and 1.11.2 are now available
* Clusters now come with managed cross-region cluster backup solution, see [Backups](../../02.Documentation/07.backups/default.en.md)
* Security groups in [LoadBalancer services](../../03.Tutorials/13.create-a-load-balancer/default.en.md) are managed automatically
* Clusters now come with managed metrics-server which enables [Horizontal Pod AutoScaling](../../03.Tutorials/19.use-horizontal-pod-autoscaling/default.en.md)
* Cluster creation: Drop-downs instead of free-text input boxes to choose from available floating IP pools, security groups, networks and subnets

## 2018-08-08

* Kubernetes versions 1.10.6 and 1.9.10 are now available

## 2018-08-01

* MetaKube is now generally available

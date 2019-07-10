---
title: 'Release Notes'
taxonomy:
    tag:
        - metakube
        - release-notes
page-toc:
    active: false
---
<!-- markdownlint-disable MD036 -->
## July 2019

**2019-07-01**

* MetaKube dashboard now shows more prominent indicators if a node deployment is outdated.

## June 2019

**2019-06-25**

* MetaKube now supports creating Kubernetes clusters on AWS (regions eu-central-1 and eu-west-1 for now), with full feature parity between clusters on SysEleven Stack and AWS.

**2019-06-24**

* The [Web Terminal](../../02.Documentation/11.metakube-webterminal/default.en.md) now contains the [LinkerD CLI](https://linkerd.io/), [fluxctl](https://www.weave.works/oss/flux/) and [kubens](https://github.com/ahmetb/kubectx)

**2019-06-20**

* On OpenStack MetaKube now also supports VMs with CPU:memory ratios of 1:2 and 1:8 in addition to the existing 1:4 flavors ([Details](https://docs.syseleven.de/syseleven-stack/en/reference/compute))

**2019-06-11**

* Updated [Helm](https://www.helm.sh/) to [2.14.1](https://github.com/helm/helm/releases/tag/v2.14.1)
* Updated the backup utility [velero](https://github.com/heptio/velero) to [1.0.0](https://github.com/heptio/velero/releases/tag/v1.0.0)
* Updated [node-exporter](https://github.com/prometheus/node_exporter) to [0.18.1](https://github.com/prometheus/node_exporter/releases/tag/v0.18.1), this also adds a service for easier monitoring with the Prometheus operator

**2019-06-05**

* The node deployment editor now automatically detects when a newer OpenStack image is available and allows the user to perform a rolling update

## May 2019

**2019-05-31**

* Kubernetes versions 1.14.2 and 1.13.6 were removed because of a [security vulnerability](https://github.com/kubernetes/kubernetes/issues/78308)
* Major network performance and reliability improvements
* [CoreDNS](https://coredns.io/) is not logging every dns query anymore

**2019-05-28**

* Kubernetes version 1.12.9 is now available

**2019-05-16**

* Updated [Helm](https://www.helm.sh/) to [2.14.0](https://github.com/helm/helm/releases/tag/v2.14.0)
* When creating a NodeDeployment you can define the taints that should be attached to a node
* Kubernetes version 1.14.2 is now available

**2019-05-10**

* Kubernetes versions 1.14.1, 1.14.0, 1.13.6 and 1.12.8 are now available
* When deleting a MetaKube cluster, you can now choose if you want to delete its OpenStack LoadBalancers or not
* When creating a NodeDeployment you can define the labels that should be attached to a node
* When upgrading a MetaKube cluster, you can also automatically update all NodeDeployments to do a rolling update of all nodes
* The Kubernetes Software Defined Network canal has been updated: [calico](https://www.projectcalico.org/) to [2.6.12](https://docs.projectcalico.org/v2.6/releases/) and [flannel](https://github.com/coreos/flannel) to [0.11.0](https://github.com/coreos/flannel/releases/tag/v0.11.0). This brings several bugfixes and network performance improvements.
* Every node in a MetaKube cluster now reserves 200m CPU for system components to not overload the node and increase stability
* The Kubernetes cluster DNS service has been switched to [CoreDNS](https://coredns.io/) version [1.3.1](https://coredns.io/2019/01/13/coredns-1.3.1-release/)
* Stability improvements when creating the initial NodeDeployment after the creation of a MetaKube cluster
* Updated [metrics-server](https://github.com/kubernetes-incubator/metrics-server) to [0.3.2](https://github.com/kubernetes-incubator/metrics-server/releases/tag/v0.3.2)
* When interacting with MachineDeployments, MachineSets and Machines over the kubectl CLI, you can now use the short names `md`, `ms` and `ma`
* You can now use `kubectl scale machinedeployment` to easily scale a MachineDeployment over the kubectl CLI
* An upgrade of a MetaKube cluster is now only available, if the versions of the existing nodes would work together with the new master components version
* etcd is updated to 3.3.12
* The NodeDeployment detail page in the MetaKube dashboard now also contains Node related events
* The name of a project can now be edited
* Kubernetes master components resource requests, limits and pod affinities have been updated to increase performance and stability

## April 2019

**2019-04-02**

* The [Web Terminal](../../02.Documentation/11.metakube-webterminal/default.en.md) access is now restricted to owners and editors
* The kubeconfig download is now restricted to owners and editors

**2019-04-01**

* Kubernetes versions 1.12.7 and 1.13.5 are now available
* We now provide up to date default images for Ubuntu, CoreOS and CentOS, see [Supported Operating Systems](../../02.Documentation/04.supported-operating-systems/default.en.md) for details
* The [Web Terminal](../../02.Documentation/11.metakube-webterminal/default.en.md) now contains the alpine coreutils package

## March 2019

**2019-03-28**

* Updated [Helm](https://www.helm.sh/) to [2.13.1](https://github.com/helm/helm/releases/tag/v2.13.1)

**2019-03-25**

* MetaKube clusters can now be created in the second SysEleven OpenStack region `cbk`

**2019-03-14**

* Updated the backup utility [velero](https://github.com/heptio/velero) to [0.11.0](https://github.com/heptio/velero/releases/tag/v0.11.0). This includes a rename to [velero](https://github.com/heptio/velero)

**2019-03-05**

* OpenStack CLI tools are available in [Web Terminal](../../02.Documentation/11.metakube-webterminal/default.en.md)

**2019-03-04**

* Kubernetes versions 1.12.6 and 1.13.4 are now available
* Disabled all Kubernetes versions before 1.12.6 and 1.13.4 due to a [Kubernetes security incident](https://discuss.kubernetes.io/t/kubernetes-security-announcement-v1-11-8-1-12-6-1-13-4-released-to-address-medium-severity-cve-2019-1002100/5147), all existing clusters have been upgraded automatically
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.10.1](https://github.com/heptio/ark/releases/tag/v0.10.1)
* Updated [Helm](https://www.helm.sh/) to [2.13.0](https://github.com/helm/helm/releases/tag/v2.13.0)

## February 2019

**2019-02-15**

* The dashboard shows now the Kernel and Container Runtime Versions of nodes, which makes it easier to see if a node is affected by a security vulnerability

**2019-02-14**

* Kubernetes versions 1.10.x are not available anymore corresponding to our [Supported Versions Matrix](../../02.Documentation/03.supported-kubernetes-versions/default.en.md)
* You can now add [tags](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) to nodes when adding them
* For new clusters and new nodes in existing clusters, nodes are now wrapped inside of NodeDeployments which makes it possible to trigger rolling updates of nodes within a cluster from the dashboard
* New nodes now receive Docker 18.9.2 which fixes [CVE-2019-5736 in Runc](https://kubernetes.io/blog/2019/02/11/runc-and-cve-2019-5736/) for all new nodes
* Multiple owners can now be added to one MetaKube [project](../../02.Documentation/02.projects/default.en.md)
* Detailed events for the lifecycle of a node are shown in the dashboard

**2019-02-13**

* Every MetaKube cluster comes now with a [web-terminal](../../02.Documentation/11.metakube-webterminal/default.en.md) that is accessible from the [MetaKube dashboard](../../02.Documentation/09.metakube-dashboard/default.en.md)

**2019-02-07**

* Kubernetes version 1.13.3 is now available

## January 2019

**2019-01-23**

* Updated [Helm](https://www.helm.sh/) to [2.12.3](https://github.com/helm/helm/releases/tag/v2.12.3)

**2019-01-22**

* Kubernetes version 1.12.5 is now available
* The master service health indicators on the cluster details page now contains all services that are fully managed

**2019-01-17**

* Kubernetes versions 1.13.1 and 1.13.2 are now available

**2019-01-15**

* Updated [Helm](https://www.helm.sh/) to [2.12.2](https://github.com/helm/helm/releases/tag/v2.12.2)
* The cluster details page now comes with [a dashboard](../../02.Documentation/09.metakube-dashboard/default.en.md) showing the amount of pods in the cluster and the cpu and memory usage of the worker nodes

## December 2018

**2018-12-20**

* Kubernetes versions 1.10.12 and 1.12.4 are now available
* Updated [Helm](https://www.helm.sh/) to [2.12.1](https://github.com/helm/helm/releases/tag/v2.12.1)
* Updated [kubernetes-dashboard](../../02.Documentation/14.kubernetes-dashboard/default.en.md) to [1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1)

**2018-12-04**

* Disabled all Kubernetes versions before 1.10.11 and 1.12.3 due to a [Kubernetes security incident](https://github.com/kubernetes/kubernetes/issues/71411)

## November 2018

**2018-11-26**

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.10.0](https://github.com/heptio/ark/releases/tag/v0.10.0)
* Updated [metrics-server](https://github.com/kubernetes-incubator/metrics-server) to [0.3.1](https://github.com/kubernetes-incubator/metrics-server/releases/tag/v0.3.1)
* Kubernetes versions 1.10.11 and 1.12.3 are now available

**2018-11-19**

* MetaKube now supports [horizontal node autoscaling](../../03.Tutorials/20.use-horizontal-node-autoscaling/default.en.md)
* Kubernetes version 1.10.10 is now available

**2018-11-13**

* Allow resizing of persistent volumes. See [Resizing persistent volumes](../../03.Tutorials/21.resize-persistent-volume/default.en.md).

**2018-11-09**

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.11](https://github.com/heptio/ark/releases/tag/v0.9.11)

**2018-11-02**

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.10](https://github.com/heptio/ark/releases/tag/v0.9.10)

## October 2018

**2018-10-31**

* MetaKube now supports [projects](../../02.Documentation/02.projects/default.en.md) to group, maintain and access clusters with multiple users
* Kubernetes versions 1.10.9, 1.12.1 and 1.12.2 are now available
* etcd is updated to 3.3.9
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.9](https://github.com/heptio/ark/releases/tag/v0.9.9)
* New Ubuntu worker nodes are now using Ubuntu 18.04
* Kubernetes versions 1.11.* are now disabled due to a [bug in kubernetes](https://github.com/kubernetes/kubernetes/issues/68270) where nodes will loose their IP addresses

**2018-10-08**

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.7](https://github.com/heptio/ark/releases/tag/v0.9.7)

**2018-10-04**

* Kubernetes versions 1.9.11 and 1.10.8 are now available
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.6](https://github.com/heptio/ark/releases/tag/v0.9.6)

## September 2018

**2018-09-27**

* Clusters now come with [Helm](https://www.helm.sh/) already installed and configured. See also [Using Helm](../../03.Tutorials/17.using-helm/default.en.md).

**2018-09-18**

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.5](https://github.com/heptio/ark/releases/tag/v0.9.5) which fixes the restic integration ([Release Notes](https://github.com/heptio/ark/releases/tag/v0.9.5))

**2018-09-14**

* MetaKube is now a [Certified Hosted Kubernetes Platform](https://landscape.cncf.io/landscape=certified-kubernetes-hosted&selected=sys-eleven-meta-kube). The conformance test results are publicly available for Kubernetes [1.10](https://github.com/cncf/k8s-conformance/tree/master/v1.10/metakube) and [1.11](https://github.com/cncf/k8s-conformance/tree/master/v1.11/metakube)

**2018-09-13**

* Kubernetes versions 1.10.7 and 1.11.3 are now available

**2018-09-12**

* [kubernetes-dashboard](../../02.Documentation/14.kubernetes-dashboard/default.en.md) is updated to version [1.10.0](https://github.com/kubernetes/dashboard/releases/tag/v1.10.0)

**2018-09-05**

* The `default` namespace now comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
  that assigns a default CPU and memory request to pods that do not define one explicitly. See also [LimitRange](../../02.Documentation/08.kubernetes-limitrange/default.en.md).

## August 2018

**2018-08-22**

* Kubernetes version 1.11.1 and 1.11.2 are now available
* Clusters now come with managed cross-region cluster backup solution, see [Backups](../../02.Documentation/07.backups/default.en.md)
* Security groups in [LoadBalancer services](../../03.Tutorials/13.create-a-load-balancer/default.en.md) are managed automatically
* Clusters now come with managed metrics-server which enables [Horizontal Pod AutoScaling](../../03.Tutorials/19.use-horizontal-pod-autoscaling/default.en.md)
* Cluster creation: Drop-downs instead of free-text input boxes to choose from available floating IP pools, security groups, networks and subnets

**2018-08-08**

* Kubernetes versions 1.10.6 and 1.9.10 are now available

**2018-08-01**

* MetaKube is now generally available
<!-- markdownlint-enable MD036 -->

---
title: 'Release Notes'
taxonomy:
    tag:
        - metakube
        - release-notes
page-toc:
    active: false
---

### January 2021

#### 2021-01-04

* Provide kubernetes versions 1.18.13, 1.17.15 and 1.16.15
* Provide dynamic kubelet configuration via a configmap in the MetaKube cluster. Please consult the documentation for additional information.
* Added a second storage class (sys11-quobyte-multi) to support Read Write Many volumes (RWX). Please consult the [documentation](../../04.tutorials/31.use-multi-attach-volumes/default.en.md) for additional information.
* Updated the external cloud controller to v1.19.2 which adds support for RWX volumes and fixes a few open issues.

### November 2020

#### 2020-11-10

* Provide kubernetes versions 1.18.8, 1.17.11 and 1.16.14
* Provide ingress addon with PROXY protocol on Octavia for clusters in version 1.18.x.
* Finished migration of cluster etcds to local storage nodes.

### October 2020

#### 2020-10-20

* New clusters get created with etcd launcher.
* Added tolerations to cinder CSI plugin to be able to mount volumes in tainted nodes.
* Updated seed infrastructure nodes to Ubuntu 20.04 LTS.
* Added validation for taints when creating and updating node deployments using the Web UI.
* Fixed kubeconfig API endpoint.
* Fixed issue with kubelet restart for new/updated clusters.

### September 2020

#### 2020-09-30

* On OpenStack every Node now gets an additional label `machine-controller/physical-host-id` that contains the id of the physical server the Node is scheduled. It is meant to replace `machine-controller/host-id`.

#### 2020-09-18

* Remove datacenters without valid VM types on Azure.

#### 2020-09-09

* Update [velero](https://velero.io/) (backups) addon to [1.4.0](https://github.com/vmware-tanzu/velero/releases/tag/v1.4.0).
* Provide Octavia load balancers for clusters in version 1.18 on Openstack. [See here](https://docs.syseleven.de/syseleven-stack/en/reference/network/lbaas#octavia-lbaas) for more information on Octavia in over Syseleven Stack.

#### 2020-09-03

* Update [cert-manager](https://cert-manager.io/) addon to [0.15.2](https://cert-manager.io/docs/release-notes/release-notes-0.15/).

### August 2020

#### 2020-08-11

* Provide autoscaling and pod-anti-affinity policies for [CoreDNS](https://coredns.io/).

### July 2020

#### 2020-07-30

* New Add-on [RabbitMQ](../../03.addons/13.metakube-rabbitmq/default.en.md) has been added to provide a popular message broker.

#### 2020-07-20

* Kubernetes versions 1.16.10 and 1.17.6 got updated to [1.16.13](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.16.md#changelog-since-v11612) and [1.17.9](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.17.md#changelog-since-v1178).

#### 2020-07-14

* New Add-on [Redis](../../03.addons/14.metakube-redis/default.en.md) has been added to provide redis key-value storage.

#### 2020-07-13

* We updated MetaKube to 2.14. Which includes many new features and UI improvments. This release includes full support for FlatcarOS our replacement for CoreOS which is EOL. You can also change the UI theme to a dark or light theme.
* We also added a new feature to download the cluster usage data of your running clusters in JSON or CSV format. To ensure that you are always aware of your current usage.
* We now add a soft affinity policy to all clusters in openstack. This will help to ensure that your worker VM's are distributed between multiple compute nodes in openstack. We also add a label with the host id of the compute node to every worker. This allows you to check if the soft affinity policy is working as expected.

### June 2020

#### 2020-06-29

* [CoreDNS](https://coredns.io/) has been updated to version [1.7.0](https://coredns.io/2020/06/15/coredns-1.7.0-release/)

#### 2020-06-09

* Kubernetes versions 1.15.12, 1.16.10 and 1.17.6 are now available.
* We replaced the current CoreOS image with the new Flatcar image. CoreOS will not be further maintained by Redhat. The new Flatcar image is a friendly fork of CoreOS Container Linux and as such, compatible with it.

### May 2020

#### 2020-05-10

* etcd is updated to 3.4.7

#### 2020-05-06

* Changes of the SSH-keys do not require a rebuild of existing nodes anymore, instead they are updated in realtime.
* The MetaKube dashboard offers an easy way to manage user/group [permissions](../../04.tutorials/06.external-authentication/default.en.md).
* MetaKube supports additional Azure regions in the US and Canada.
* For new OpenStack clusters MetaKube uses the external cloud controller manager and CSI.

### April 2020

#### 2020-04-06

* New Add-on [Cluster Log Management](../../03.addons/12.metakube-logging/default.en.md) has been added to provide central log management with Loki and Grafana.
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Prometheus has been updated to version [2.17.1](https://github.com/prometheus/prometheus/releases/tag/v2.17.1)

### March 2020

#### 2020-03-30

* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc7](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc7)
* The Kubernetes Software Defined Network canal has been updated: [flannel](https://github.com/coreos/flannel) to [0.12.0](https://github.com/coreos/flannel/releases/tag/v0.12.0). This brings several bugfixes and network performance improvements.

#### 2020-03-25

* MetaKube now supports creating Kubernetes clusters on Azure regions westeurope, northeurope, francecentral, uksouth, ukwest, germanywestcentral, switzerlandnorth and norwayeast
* You can now configure authentication at MetaKube clusters with [SysEleven Login](../../04.tutorials/06.external-authentication/default.en.md)

#### 2020-03-24

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.7.1](https://github.com/grafana/grafana/releases/tag/v6.7.1)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc6](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc6)
* The Add-On [weave-scope](../../03.addons/05.weave-scope/default.en.md) has been updated to version [1.13.0](https://github.com/weaveworks/scope/releases/tag/1.13.0)

#### 2020-03-18

* The Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md) has been updated to version [0.7.0](https://github.com/kubernetes-incubator/external-dns/releases/tag/v0.7.0)
* The Add-On [Nginx Ingress Controller](../../03.addons/01.metakube-ingress/default.en.md) was updated to version [0.30.0](https://github.com/kubernetes/ingress-nginx/releases/tag/nginx-0.30.0)
* The Add-On [velero](https://github.com/heptio/velero) has been updated to version [1.3.1](https://github.com/heptio/velero/releases/tag/v1.3.1)
* The Kubernetes Software Defined Network canal has been updated: [calico](https://www.projectcalico.org/) to [3.13.0](https://docs.projectcalico.org/release-notes/). This brings several bugfixes and network performance improvements.
* Kubernetes versions 1.15.11, 1.16.8 and 1.17.4 are now available.

#### 2020-03-09

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.6.2](https://github.com/grafana/grafana/releases/tag/v6.6.2)
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Prometheus has been updated to version [2.16.0](https://github.com/prometheus/prometheus/releases/tag/v2.16.0)
* The Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md) has been updated to version [0.6.0](https://github.com/kubernetes-incubator/external-dns/releases/tag/v0.6.0)

### February 2020

#### 2020-02-26

* Kubernetes versions 1.15.10, 1.16.7 and 1.17.3 are now available.

#### 2020-02-11

* When creating a cluster you can now change the IP ranges for Pods, Services and Nodes

#### 2020-02-10

* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Linkerd has been updated to version [2.7.0](https://github.com/linkerd/linkerd2/releases/tag/stable-2.7.0)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc5](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc5)

#### 2020-02-07

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.6.1](https://github.com/grafana/grafana/releases/tag/v6.6.1)
* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Flagger has been updated to version [0.23.0](https://github.com/weaveworks/flagger/releases/tag/0.23.0)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc4](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc4)

#### 2020-02-04

* Each cluster now runs [node-problem-detector](https://github.com/kubernetes/node-problem-detector) with
  a customization that sets an initial taint on each newly created node that will only be removed when
  pod networking and dns are up and running on the node. This prevents pods, especially from Jobs or CronJobs,
  from being scheduled and started even though the node has no working DNS or network.

### January 2020

#### 2020-01-31

* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc3](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc3)
* The Kubernetes Software Defined Network canal has been updated: [calico](https://www.projectcalico.org/) to [3.12.0](https://docs.projectcalico.org/release-notes/). This brings several bugfixes and network performance improvements.

#### 2020-01-28

* [Horizontal node autoscaling](../../04.tutorials/20.use-horizontal-node-autoscaling/default.en.md) is now configurable from the MetaKube dashboard.
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.6.0](https://github.com/grafana/grafana/releases/tag/v6.6.0)

#### 2020-01-24

* Kubernetes versions 1.15.9, 1.16.6 and 1.17.2 are now available.
* We limited the Docker max log size to 100mb per container.

#### 2020-01-17

* On clusters with CoreOS Container Linux nodes, the time window where nodes are rebooted after an update is now [configurable](../../02.documentation/04.supported-operating-systems/default.en.md)
* The Add-On [Nginx Ingress Controller](../../03.addons/01.metakube-ingress/default.en.md) was updated to version [0.27.1](https://github.com/kubernetes/ingress-nginx/releases/tag/nginx-0.27.1)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc2](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc2)
* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Flagger has been updated to version [0.22.0](https://github.com/weaveworks/flagger/releases/tag/0.22.0)

#### 2020-01-10

* Kubernetes versions 1.15.7, 1.16.4 and 1.17.0 are now available.
* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Flagger has been updated to version [0.21.0](https://github.com/weaveworks/flagger/releases/tag/0.21.0)

#### 2020-01-09

* We extended our Cluster Monitoring Stack Add-On. The Add-On now creates a persistent volume to store your Grafana users and plugins. You can still continue to use ConfigMaps if you prefer to.
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Prometheus has been updated to version [2.15.2](https://github.com/prometheus/prometheus/releases/tag/v2.15.2)
* For Kubernetes 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-rc1](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-rc1)

#### 2020-01-08

* The Kubernetes Software Defined Network canal has been updated: [calico](https://www.projectcalico.org/) to [3.11.1](https://docs.projectcalico.org/v3.11/releases/). This brings several bugfixes and network performance improvements.

#### 2020-01-07

* The CoreDNS configuration in a MetaKube cluster can now be [extended with a ConfigMap](../../02.documentation/15.dns/default.en.md)
* When creating a new Node, taints from the NodeDeployment are now added synchronously during the registration of the Node instead of asynchronously shortly after

### December 2019

#### 2019-12-20

* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Linkerd has been updated to version [2.6.1](https://github.com/linkerd/linkerd2/releases/tag/stable-2.6.1)
* In the Add-On [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) Flagger has been updated to version [0.20.4](https://github.com/weaveworks/flagger/releases/tag/0.20.4)
* Linkerd can now also be installed in a non-highly-available mode

#### 2019-12-19

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.5.2](https://github.com/grafana/grafana/releases/tag/v6.5.2)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-beta8](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-beta8)
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) alertmanager has been updated to version [0.20.0](https://github.com/prometheus/alertmanager/releases/tag/v0.20.0)

#### 2019-12-17

* On OpenStack every Node now gets an additional label `machine-controller/host-id` that contains the id of the physical server the Node is scheduled on.

#### 2019-12-02

* Kubernetes version 1.16.3 is now available.
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.5.1](https://github.com/grafana/grafana/releases/tag/v6.5.1)
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Prometheus has been updated to version [2.14.0](https://github.com/prometheus/prometheus/releases/tag/v2.14.0)
* The [Calico](https://www.projectcalico.org/) CNI Plugin received an update to the version [3.10.1](https://docs.projectcalico.org/v3.10/release-notes/)
* For Kubernetes >= 1.16 clusters the [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) has been updated to [2.0.0-beta6](https://github.com/kubernetes/dashboard/releases/tag/v2.0.0-beta6)
* It is now possible to activate audit logging in a MetaKube cluster
* AWS clusters can now have NodeDeployments in different availability zones of the chosen region
* Labels can now be added to MetaKube projects and clusters. Nodes inherit these labels automatically
* There is now an indicator that warns you while editing a NodeDeployment if the edit would result in recreating the nodes

### November 2019

#### 2019-11-26

* Kubernetes versions 1.14.9 and 1.15.6 are now available.
* MetaKube now supports creating Kubernetes clusters on AWS regions eu-west-2, eu-west-3 and eu-north-1

#### 2019-11-20

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has now also the plugin [grafana-piechart-panel](https://grafana.com/grafana/plugins/grafana-piechart-panel) pre-installed

#### 2019-11-18

* In the Add-On [Vault](../../03.addons/11.metakube-vault/default.en.md)  Consul has been updated to version [1.6.2](https://www.hashicorp.com/blog/hashicorp-consul-1-6/)

#### 2019-11-14

* Updated [Helm](https://www.helm.sh/) to [2.16.1](https://github.com/helm/helm/releases/tag/v2.16.1)
* The kubelet has been patched to address an [issue](https://github.com/kubernetes/kubernetes/issues/80968), where a Pod can be stuck in NotReady. We advise to rebuild your worker nodes so that the patched version gets installed.

#### 2019-11-12

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.4.4](https://github.com/grafana/grafana/releases/tag/v6.4.4)
* The Add-On [velero](https://github.com/heptio/velero) has been updated to version [1.2.0](https://github.com/heptio/velero/releases/tag/v1.2.0)
* [CoreDNS](https://coredns.io/) has been updated to version [1.6.5](https://coredns.io/2019/11/05/coredns-1.6.5-release/)

### October 2019

#### 2019-10-31

* New Add-on [Linkerd](../../03.addons/10.metakube-linkerd/default.en.md) has been added to enable service mesh in metakube clusters.
* New Add-on [Vault](../../03.addons/11.metakube-vault/default.en.md) has been added to allow you to secure, store and control access to tokens, passwords, certificates and encryption codes. With Vault you can protect your secrets and other sensitive data.
* Updated [Helm](https://www.helm.sh/) to [2.15.2](https://github.com/helm/helm/releases/tag/v2.15.2)

#### 2019-10-28

* Updated [Helm](https://www.helm.sh/) to [2.15.1](https://github.com/helm/helm/releases/tag/v2.15.1)

#### 2019-10-22

* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.4.3](https://github.com/grafana/grafana/releases/tag/v6.4.3)
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Prometheus has been updated to version [2.13.1](https://github.com/prometheus/prometheus/releases/tag/v2.13.1)
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) prometheus-operator has been updated to version [0.32.0](https://github.com/coreos/prometheus-operator/releases/tag/v0.32.0)
* Kubernetes versions 1.13.12, 1.14.8 and 1.15.5 are now available. This fixes CVE-2019-11253, which renders the Kubernetes API server vulnerable to a denial-of-service attack [Security Advisory](https://blog.paloaltonetworks.com/2019/10/cloud-kubernetes-vulnerabilities/)

#### 2019-10-17

* The Add-On [cert-manager](../../03.addons/08.metakube-cert-manager/default.en.md) has been updated to version [0.10.1](https://github.com/jetstack/cert-manager/releases/tag/v0.10.1)
* The Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md) has now configuration options for a domain filter and the Cloudflare proxy feature
* The Add-On [Nginx Ingress Controller](../../03.addons/01.metakube-ingress/default.en.md) has now a configuration option for a default tls certificate
* etcd is updated to 3.4.1

#### 2019-10-10

* The Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md) has been updated to version [0.5.17](https://github.com/kubernetes-incubator/external-dns/releases/tag/v0.5.17)
* [CoreDNS](https://coredns.io/) has been updated to version [1.6.4](https://coredns.io/2019/09/27/coredns-1.6.4-release/)


#### 2019-10-04

* Kubernetes versions 1.13.11, 1.14.7 and 1.15.4 are now available
* In the Add-On [Cluster Monitoring Stack](../../03.addons/03.metakube-monitoring/default.en.md) Grafana has been updated to version [6.3.6](https://github.com/grafana/grafana/releases/tag/v6.3.6)

### September 2019

#### 2019-09-23

* The Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md) supports now [RcodeZero AnyCast DNS](https://www.rcodezero.at/)
* Beta test for authentication with SysEleven Login started

#### 2019-09-05

* Node deployments on OpenStack can now be created without floating IPs. For the implications have a look at our [cluster-api documentation](../../02.documentation/12.cluster-api/default.en.md).
* The stable channel of the Add-On [velero](https://github.com/heptio/velero) has been updated to version [1.1.0](https://github.com/heptio/velero/releases/tag/v1.1.0)

#### 2019-09-03

* Added Add-On [external-dns](../../03.addons/09.metakube-external-dns/default.en.md)
* Added Add-On [cert-manager](../../03.addons/08.metakube-cert-manager/default.en.md)

#### 2019-09-02

* CentOS is now removed as a supported OS for new clusters and NodeDeployments, we recommend you also change your existing NodeDeployments to either Ubuntu or CoreOS to improve performance and stability due to newer Linux kernels in these images.

### August 2019

#### 2019-08-29

* The beta channel of the Add-On [velero](https://github.com/heptio/velero) has been updated to version [1.1.0](https://github.com/heptio/velero/releases/tag/v1.1.0)

#### 2019-08-22

* On OpenStack clusters the root disk size is now configurable for network storage flavors

#### 2019-08-21

* The [Calico](https://www.projectcalico.org/) CNI Plugin received an update to the version [3.8.2](https://docs.projectcalico.org/v3.8/release-notes/)
* [CoreDNS](https://coredns.io/) has been updated to version [1.6.2](https://coredns.io/2019/08/13/coredns-1.6.2-release/)

#### 2019-08-20

* Kubernetes versions 1.13.10, 1.14.6 and 1.15.3 are now available, because of a Denial-of-Service vulnerability in the HTTP/2 stack all clusters have been updated to the next patch version automatically. For details on the vulnerability see [Security Advisory NFLX-2019-002](https://github.com/Netflix/security-bulletins/blob/master/advisories/third-party/2019-002.md)
* The Add-On [Nodelocal DNS Cache](https://docs.syseleven.de/metakube/en/documentation/dns) has been updated to version 1.15.3

#### 2019-08-19

* [Pod Security Policies](../../02.documentation/16.pod-security-policy/default.en.md) can now be activated in a MetaKube cluster
* The Add-On [Nginx Ingress Controller](../../03.addons/01.metakube-ingress/default.en.md) was updated to version [0.25.1](https://github.com/kubernetes/ingress-nginx/releases/tag/nginx-0.25.1) with fixes for for CVE-2018-16843, CVE-2018-16844, CVE-2019-9511, CVE-2019-9513, and CVE-2019-9516

#### 2019-08-14

* The [Calico](https://www.projectcalico.org/) CNI Plugin received an update to the version 3.8.0

#### 2019-08-07

* Added experimental [Node Optimizations](../../03.addons/07.metakube-node-optimization/default.en.md) Add-On. This Add-On configures system settings to optimize your virtual machine for using docker containers under load. This should improve your general system and network performance.
* Added [MetaKube Monitoring](../../03.addons/03.metakube-monitoring/default.en.md) Add-On (Prometheus Operator). The Prometheus Operator deloys a full monitoring stack. Including Grafana, Prometheus and the Alertmanager. You can configure several setting like the retention period of your data. You can also configure a mail address or a slack channel to recieve your alerts.

#### 2019-08-06

* Kubernetes versions 1.13.9, 1.14.5 and 1.15.2 are now available, because of two security vulnerabilities in previous releases clusters should be updated to the newest patch release. See [#80983](https://github.com/kubernetes/kubernetes/issues/80983) and [#80984](https://github.com/kubernetes/kubernetes/issues/80984) for details
* The name of a cluster can now be changed from the dashboard
* Added default Add-On [Nodelocal DNS Cache](https://docs.syseleven.de/metakube/en/documentation/dns)

#### 2019-08-05

* Previously [Velero](../../03.addons/02.metakube-backups/default.en.md) was installed in every cluster, now it can be installed manually as an Add-On

#### 2019-08-02

* [CoreDNS](https://coredns.io/) has been updated to version [1.6.1](https://coredns.io/2019/08/01/coredns-1.6.1-release/)

#### 2019-08-01

* [CoreDNS](https://coredns.io/) has been updated to version [1.6.0](https://coredns.io/2019/07/28/coredns-1.6.0-release//)

### July 2019

#### 2019-07-31

* Previously the [Web Terminal](../../03.addons/04.metakube-webterminal/default.en.md) was installed in every cluster, now it can be installed manually as an Add-On
* Updated [Helm](https://www.helm.sh/) to [2.14.3](https://github.com/helm/helm/releases/tag/v2.14.3)
* Add-Ons can now directly be installed during cluster creation

#### 2019-07-26

* [MetaKube Add-Ons](../../03.addons/default.en.md) are now in beta. Add-Ons are fully managed applications that can be installed into your cluster
* Added Add-On [Nginx Ingress Controller](../../03.addons/01.metakube-ingress/default.en.md)
* Added Add-On [Weave Scope](../../03.addons/05.weave-scope/default.en.md)
* Added Add-On [Kubernetes Dasbhoard](../../03.addons/06.kubernetes-dashboard/default.en.md)
* Previously the Kubernetes Dashboard was installed in every cluster, now it can be installed manually as an Add-On
* Stability and performance improvements when deleting persistent volumes during cluster deletion

#### 2019-07-25

* Add Swagger based [API Docs](../../02.documentation/14.metakube-api/default.en.md)

#### 2019-07-22

* Kubernetes versions 1.12.10, 1.13.8, 1.14.4, 1.15.0 and 1.15.1 are now available
* Updated [Helm](https://www.helm.sh/) to [2.14.2](https://github.com/helm/helm/releases/tag/v2.14.2)
* On Openstack, the scheduler is now aware that only 25 additional volumes may be attached to a node. It will make sure that pods with mounted volumes are only scheduled on nodes that can support them.
* There are now Api Accounts that can be used to authenticate with the [MetaKube API](../../02.documentation/14.metakube-api/default.en.md) to automate management of clusters
* More details are shown when using kubectl get machine/machineset/machinedeployment
* ICMP traffic to clusters is now always permitted to allow MTU discovery
* Cluster events are now visible on the Cluster Detail Page
* Various design and usability improvements

#### 2019-07-10

* MetaKube kubeconfig files now use a unique context and username to simplify usage of them.

#### 2019-07-01

* MetaKube dashboard now shows more prominent indicators if a node deployment is outdated.

### June 2019

#### 2019-06-25

* MetaKube now supports creating Kubernetes clusters on AWS (regions eu-central-1 and eu-west-1 for now), with full feature parity between clusters on SysEleven Stack and AWS.

#### 2019-06-24

* The [Web Terminal](../../03.addons/04.metakube-webterminal/default.en.md) now contains the [LinkerD CLI](https://linkerd.io/), [fluxctl](https://www.weave.works/oss/flux/) and [kubens](https://github.com/ahmetb/kubectx)

#### 2019-06-20

* On OpenStack MetaKube now also supports VMs with CPU:memory ratios of 1:2 and 1:8 in addition to the existing 1:4 flavors ([Details](https://docs.syseleven.de/syseleven-stack/en/reference/compute))

#### 2019-06-11

* Updated [Helm](https://www.helm.sh/) to [2.14.1](https://github.com/helm/helm/releases/tag/v2.14.1)
* Updated the backup utility [velero](https://github.com/heptio/velero) to [1.0.0](https://github.com/heptio/velero/releases/tag/v1.0.0)
* Updated [node-exporter](https://github.com/prometheus/node_exporter) to [0.18.1](https://github.com/prometheus/node_exporter/releases/tag/v0.18.1), this also adds a service for easier monitoring with the Prometheus operator

#### 2019-06-05

* The node deployment editor now automatically detects when a newer OpenStack image is available and allows the user to perform a rolling update

### May 2019

#### 2019-05-31

* Kubernetes versions 1.14.2 and 1.13.6 were removed because of a [security vulnerability](https://github.com/kubernetes/kubernetes/issues/78308)
* Major network performance and reliability improvements
* [CoreDNS](https://coredns.io/) is not logging every dns query anymore

#### 2019-05-28

* Kubernetes version 1.12.9 is now available

#### 2019-05-16

* Updated [Helm](https://www.helm.sh/) to [2.14.0](https://github.com/helm/helm/releases/tag/v2.14.0)
* When creating a NodeDeployment you can define the taints that should be attached to a node
* Kubernetes version 1.14.2 is now available

#### 2019-05-10

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

### April 2019

#### 2019-04-02

* The [Web Terminal](../../03.addons/04.metakube-webterminal/default.en.md) access is now restricted to owners and editors
* The kubeconfig download is now restricted to owners and editors

#### 2019-04-01

* Kubernetes versions 1.12.7 and 1.13.5 are now available
* We now provide up to date default images for Ubuntu, CoreOS and CentOS, see [Supported Operating Systems](../../02.documentation/04.supported-operating-systems/default.en.md) for details
* The [Web Terminal](../../03.addons/04.metakube-webterminal/default.en.md) now contains the alpine coreutils package

### March 2019

#### 2019-03-28

* Updated [Helm](https://www.helm.sh/) to [2.13.1](https://github.com/helm/helm/releases/tag/v2.13.1)

#### 2019-03-25

* MetaKube clusters can now be created in the second SysEleven OpenStack region `cbk`

#### 2019-03-14

* Updated the backup utility [velero](https://github.com/heptio/velero) to [0.11.0](https://github.com/heptio/velero/releases/tag/v0.11.0). This includes a rename to [velero](https://github.com/heptio/velero)

#### 2019-03-05

* OpenStack CLI tools are available in [Web Terminal](../../03.addons/04.metakube-webterminal/default.en.md)

#### 2019-03-04

* Kubernetes versions 1.12.6 and 1.13.4 are now available
* Disabled all Kubernetes versions before 1.12.6 and 1.13.4 due to a [Kubernetes security incident](https://discuss.kubernetes.io/t/kubernetes-security-announcement-v1-11-8-1-12-6-1-13-4-released-to-address-medium-severity-cve-2019-1002100/5147), all existing clusters have been upgraded automatically
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.10.1](https://github.com/heptio/ark/releases/tag/v0.10.1)
* Updated [Helm](https://www.helm.sh/) to [2.13.0](https://github.com/helm/helm/releases/tag/v2.13.0)

### February 2019

#### 2019-02-15

* The dashboard shows now the Kernel and Container Runtime Versions of nodes, which makes it easier to see if a node is affected by a security vulnerability

#### 2019-02-14

* Kubernetes versions 1.10.x are not available anymore corresponding to our [Supported Versions Matrix](../../02.documentation/03.supported-kubernetes-versions/default.en.md)
* You can now add [tags](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) to nodes when adding them
* For new clusters and new nodes in existing clusters, nodes are now wrapped inside of NodeDeployments which makes it possible to trigger rolling updates of nodes within a cluster from the dashboard
* New nodes now receive Docker 18.9.2 which fixes [CVE-2019-5736 in Runc](https://kubernetes.io/blog/2019/02/11/runc-and-cve-2019-5736/) for all new nodes
* Multiple owners can now be added to one MetaKube [project](../../02.documentation/02.projects/default.en.md)
* Detailed events for the lifecycle of a node are shown in the dashboard

#### 2019-02-13

* Every MetaKube cluster comes now with a [web-terminal](../../03.addons/04.metakube-webterminal/default.en.md) that is accessible from the [MetaKube dashboard](../../02.documentation/09.metakube-dashboard/default.en.md)

#### 2019-02-07

* Kubernetes version 1.13.3 is now available

### January 2019

#### 2019-01-23

* Updated [Helm](https://www.helm.sh/) to [2.12.3](https://github.com/helm/helm/releases/tag/v2.12.3)

#### 2019-01-22

* Kubernetes version 1.12.5 is now available
* The master service health indicators on the cluster details page now contains all services that are fully managed

#### 2019-01-17

* Kubernetes versions 1.13.1 and 1.13.2 are now available

#### 2019-01-15

* Updated [Helm](https://www.helm.sh/) to [2.12.2](https://github.com/helm/helm/releases/tag/v2.12.2)
* The cluster details page now comes with [a dashboard](../../02.documentation/09.metakube-dashboard/default.en.md) showing the amount of pods in the cluster and the cpu and memory usage of the worker nodes

### December 2018

#### 2018-12-20

* Kubernetes versions 1.10.12 and 1.12.4 are now available
* Updated [Helm](https://www.helm.sh/) to [2.12.1](https://github.com/helm/helm/releases/tag/v2.12.1)
* Updated [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) to [1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1)

#### 2018-12-04

* Disabled all Kubernetes versions before 1.10.11 and 1.12.3 due to a [Kubernetes security incident](https://github.com/kubernetes/kubernetes/issues/71411)

### November 2018

#### 2018-11-26

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.10.0](https://github.com/heptio/ark/releases/tag/v0.10.0)
* Updated [metrics-server](https://github.com/kubernetes-incubator/metrics-server) to [0.3.1](https://github.com/kubernetes-incubator/metrics-server/releases/tag/v0.3.1)
* Kubernetes versions 1.10.11 and 1.12.3 are now available

#### 2018-11-19

* MetaKube now supports [horizontal node autoscaling](../../04.tutorials/20.use-horizontal-node-autoscaling/default.en.md)
* Kubernetes version 1.10.10 is now available

#### 2018-11-13

* Allow resizing of persistent volumes. See [Resizing persistent volumes](../../04.tutorials/21.resize-persistent-volume/default.en.md).

#### 2018-11-09

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.11](https://github.com/heptio/ark/releases/tag/v0.9.11)

#### 2018-11-02

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.10](https://github.com/heptio/ark/releases/tag/v0.9.10)

### October 2018

#### 2018-10-31

* MetaKube now supports [projects](../../02.documentation/02.projects/default.en.md) to group, maintain and access clusters with multiple users
* Kubernetes versions 1.10.9, 1.12.1 and 1.12.2 are now available
* etcd is updated to 3.3.9
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.9](https://github.com/heptio/ark/releases/tag/v0.9.9)
* New Ubuntu worker nodes are now using Ubuntu 18.04
* Kubernetes versions 1.11 are now disabled due to a [bug in kubernetes](https://github.com/kubernetes/kubernetes/issues/68270) where nodes will loose their IP addresses

#### 2018-10-08

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.7](https://github.com/heptio/ark/releases/tag/v0.9.7)

#### 2018-10-04

* Kubernetes versions 1.9.11 and 1.10.8 are now available
* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.6](https://github.com/heptio/ark/releases/tag/v0.9.6)

### September 2018

#### 2018-09-27

* Clusters now come with [Helm](https://www.helm.sh/) already installed and configured. See also [Using Helm](../../04.tutorials/17.using-helm/default.en.md).

#### 2018-09-18

* Updated the backup utility [ark](https://github.com/heptio/ark) to [0.9.5](https://github.com/heptio/ark/releases/tag/v0.9.5) which fixes the restic integration ([Release Notes](https://github.com/heptio/ark/releases/tag/v0.9.5))

#### 2018-09-14

* MetaKube is now a [Certified Hosted Kubernetes Platform](https://landscape.cncf.io/landscape=certified-kubernetes-hosted&selected=sys-eleven-meta-kube). The conformance test results are publicly available for Kubernetes [1.10](https://github.com/cncf/k8s-conformance/tree/master/v1.10/metakube) and [1.11](https://github.com/cncf/k8s-conformance/tree/master/v1.11/metakube)

#### 2018-09-13

* Kubernetes versions 1.10.7 and 1.11.3 are now available

#### 2018-09-12

* [kubernetes-dashboard](../../03.addons/06.kubernetes-dashboard/default.en.md) is updated to version [1.10.0](https://github.com/kubernetes/dashboard/releases/tag/v1.10.0)

#### 2018-09-05

* The `default` namespace now comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
that assigns a default CPU and memory request to pods that do not define one explicitly. See also [LimitRange](../../02.documentation/08.kubernetes-limitrange/default.en.md).

### August 2018

#### 2018-08-22

* Kubernetes version 1.11.1 and 1.11.2 are now available
* Clusters now come with managed cross-region cluster backup solution, see [Backups](../../02.documentation/07.backups/default.en.md)
* Security groups in [LoadBalancer services](../../04.tutorials/13.create-a-load-balancer/default.en.md) are managed automatically
* Clusters now come with managed metrics-server which enables [Horizontal Pod AutoScaling](../../04.tutorials/19.use-horizontal-pod-autoscaling/default.en.md)
* Cluster creation: Drop-downs instead of free-text input boxes to choose from available floating IP pools, security groups, networks and subnets

#### 2018-08-08

* Kubernetes versions 1.10.6 and 1.9.10 are now available

#### 2018-08-01

* MetaKube is now generally available

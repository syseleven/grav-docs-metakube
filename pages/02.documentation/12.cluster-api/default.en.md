---
title: 'Cluster Management API'
published: true
taxonomy:
    tag:
        - scaling
        - kubernetes
        - cli
---

MetaKube uses the Kubernetes [Cluster Management API](https://github.com/kubernetes-sigs/cluster-api) to manage OpenStack VMs that become nodes in the Kubernetes Cluster.
This API defines the Kubernetes resources machineDeployment, machineSet and machine to manage Kubernetes nodes. These resources behave similarly to Deployments, ReplicaSets and Pods for containers.

## machines

A machine is a wrapper object for an OpenStack VM. This resource will represent a to be expected node in the kubernetes cluster. When the machineController can't find a node matching a machine it will create a new node and also delete nodes, which do not have a matching machine. You normally do not interact with machines directly.

## machineSets

A machineSet groups one or more machines (replicas) together and allows to easily scale this set of machines up and down by modifying the replica count. Usually you don't interact with machineSets directly.

## machineDeployments

machineDeployments wrap a rolling update mechanism around machineSets. This means that when you edit the machine template in a machineDeployment, the machine-controller will create new VMs matching the new specification and delete old VMs while ensuring that enough nodes are in the cluster to satisfy your the update strategy defined in the machineDeployment. For more information have a look at [how to manage node deployments](../../04.tutorials/09.manage-node-deployments/default.en.md).

They also provide autoscaling support through the [node autoscaler](../../04.tutorials/20.use-horizontal-node-autoscaling/default.en.md).

### Settings

#### Name

The name of the machineDeployment. The machines will have this name as part of their name. Leave empty for server side generation.

#### Replicas

The amount of machines in this machineDeployment. For a production cluster we recommend at least 3 replicas.

#### Kubelet Version

The version of the kubelet. You can [upgrade the master- and node-versions](../../04.tutorials/03.upgrade-a-cluster/default.en.md) independently, but need to have in mind, that kubernetes supports only one major version difference between API and kubelet.

#### Flavor and type

##### Flavor Type

You can choose between network storage and local storage. While local storage is generally faster, it can not be recovered in case of hardware failure.

##### Flavor

The flavor defines the sizing of the nodes. We provide an [overview of all flavors](https://docs.syseleven.de/syseleven-stack/en/reference/compute#flavors) in the SysEleven Stack docs.

##### Custom disk (only network storage)

You can choose to use a network volume of custom size for the root filesystem of your nodes.

!!! While the default disk is free of charge, you will be billed for the complete disk, if you choose a custom size - even if the disk size is smaller than the default size.

#### Allocate Floating IP

You can choose to allocate floating IPs on all of your nodes.

!!! If you disable the floating IPs for your machines, you can not directly access your nodes anymore. Thus you can not SSH directly into the machines, daemonSets which require host-network might not work as expected, etc. To access your nodes via SSH you should create a jumphost in the same network.

#### Image

The image field is pre-filled with the latest image provided by us for the selected operating system. You can choose to use your own image.

#### Node Labels & Taints

You can choose a set of labels and taints, which will be added to each node of the node deployment. Have a look at [Taints and Tolerations](../10.taints-and-tolerations/default.en.md) for the implications.

### API specification

A machineDeployment spec will look like the following:

```yaml
apiVersion: cluster.k8s.io/v1alpha1
kind: machineDeployment
metadata:
  annotations:
    machineDeployment.clusters.k8s.io/revision: "2"
  creationTimestamp: "2019-02-18T18:10:57Z"
  generateName: metakube-92r62cx8j6-
  generation: 2
  name: metakube-92r62cx8j6-xmljg
  namespace: kube-system
  resourceVersion: "4015"
  selfLink: /apis/cluster.k8s.io/v1alpha1/namespaces/kube-system/machineDeployments/metakube-92r62cx8j6-xmljg
  uid: 89e8b9f0-33a8-11e9-9e48-0a580af40383
spec:
  minReadySeconds: 0
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 1
  selector:
    matchLabels:
      machine: md-92r62cx8j6-cc45b75qqf
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        machine: md-92r62cx8j6-cc45b75qqf
    spec:
      metadata:
        creationTimestamp: null
      providerSpec:
        value:
          cloudProvider: openstack
          cloudProviderSpec:
            availabilityZone: dbl1
            domainName: ""
            flavor: m1.small
            floatingIpPool: ext-net
            identityEndpoint: https://api.dbl.cloud.syseleven.net:5000/v3
            image: Ubuntu Bionic 18.04 (2019-03-27)
            network: metakube-92r62cx8j6
            password: ""
            region: dbl
            securityGroups:
            - metakube-92r62cx8j6
            subnet: 86db31c3-d5dd-4a88-bc92-ab8a148062c8
            tags:
              metakube-cluster: 92r62cx8j6
            tenantName: ""
            tokenId: ""
            username: ""
          operatingSystem: ubuntu
          operatingSystemSpec:
            distUpgradeOnBoot: false
          sshPublicKeys: []
      versions:
        kubelet: 1.13.3
```

The most common fields to change are:

* `spec.replicas`: The amount of worker nodes managed by this set
* `spec.template.providerSpec.value.cloudProvicerspec.flavor`: The OpenStack flavor
* `spec.template.providerSpec.value.cloudProvicerspec.image`: The OpenStack image
* `spec.template.providerSpec.value.operatingSystem`: The operating system of the virtual machine

When the machineDeployment gets updated the machineController will take care of updating the respective machines. This is per default done in a rolling update type, which means that a new machine will be created matching the new spec, and as soon as this machine is ready an old one will be cordoned, drained and deleted. This will repeat until all machines match the new spec.

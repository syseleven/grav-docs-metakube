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

machineDeployments wrap a rolling update mechanism around machineSets. This means that when you edit the machine template in a machineDeployment, the machine-controller will create new VMs matching the new specification and delete old VMs while ensuring that enough nodes are in the cluster to satisfy your the update strategy defined in the machineDeployment. For more information have a look at [how to manage node deployments](../../03.Tutorials/09.manage-node-deployments/default.en.md).

They also provide autoscaling support through the [node autoscaler](../../03.Tutorials/20.use-horizontal-node-autoscaling/default.en.md).

### API specification

A machineDeployment spec will look like the following:

```yaml
apiVersion: cluster.k8s.io/v1alpha1
kind: machineDeployment
metadata:
  annotations:
    machineDeployment.clusters.k8s.io/revision: "2"
  creationTimestamp: "2019-02-18T18:10:57Z"
  generateName: kubermatic-92r62cx8j6-
  generation: 2
  name: kubermatic-92r62cx8j6-xmljg
  namespace: kube-system
  resourceVersion: "4015"
  selfLink: /apis/cluster.k8s.io/v1alpha1/namespaces/kube-system/machineDeployments/kubermatic-92r62cx8j6-xmljg
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
            flavor: m1.micro
            floatingIpPool: ext-net
            identityEndpoint: https://api.dbl.cloud.syseleven.net:5000/v3
            image: Ubuntu 18.04 LTS sys11 optimized - 2018-08-13
            network: kubermatic-92r62cx8j6
            password: ""
            region: dbl
            securityGroups:
            - kubermatic-92r62cx8j6
            subnet: 86db31c3-d5dd-4a88-bc92-ab8a148062c8
            tags:
              kubermatic-cluster: 92r62cx8j6
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

---
title: 'Manage worker nodes via CLI'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - cli
        - node
---

Worker nodes can be managed with our web UI as described in [manage node deployments](../09.manage-node-deployments/default.en.md). Once you installed [kubectl](../07.using-kubectl/default.en.md), you can also manage them via Command-Line-Interface (CLI) in order to automate creation, deletion and upgrades of nodes.

## List all available nodes

To get a list of all nodes execute:

```bash
kubectl get nodes -o wide
```

Every node is managed by a machine resource in the `kube-system` namespace, which are bundled into machinedeployments. To list all machinedeployment resources, execute:

```bash
kubectl get machinedeployments --namespace kube-system
```

## Manage sets worker nodes

When you want to change a machinedeployment you can edit the machinedeployment resource directly:

```bash
kubectl edit machinedeployment ${machinedeployment} --namespace kube-system
```

A machinedeployment looks like the following:

```yaml
apiVersion: cluster.k8s.io/v1alpha1
kind: MachineDeployment
metadata:
  annotations:
    machinedeployment.clusters.k8s.io/revision: "2"
  creationTimestamp: "2019-02-18T18:10:57Z"
  generateName: kubermatic-92r62cx8j6-
  generation: 2
  name: kubermatic-92r62cx8j6-xmljg
  namespace: kube-system
  resourceVersion: "4015"
  selfLink: /apis/cluster.k8s.io/v1alpha1/namespaces/kube-system/machinedeployments/kubermatic-92r62cx8j6-xmljg
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

When you edit a field in the resource the machinecontroller will take care of updating the nodes as described in the (update-) strategy. Per default it does a one-by-one rolling upgrade of all machines. Therefor it will create a new node and when this node is ready will drain and delete an old node and so on until all nodes are updated. The most common fields to update in the machinedeployment are:

* `spec.replicas`: The amount of worker nodes managed by this set
* `spec.template.providerSpec.value.cloudProvicerspec.flavor`: The OpenStack flavor
* `spec.template.providerSpec.value.cloudProvicerspec.image`: The OpenStack image
* `spec.template.providerSpec.value.operatingSystem`: The operating system of the virtual machine

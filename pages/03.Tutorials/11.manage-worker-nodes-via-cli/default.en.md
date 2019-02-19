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

Worker nodes can be managed with our web UI as described in [manage node deployments](../09.manage-node-deployments/default.en.md). Once you have installed [kubectl](../07.using-kubectl/default.en.md), you can also manage them via Command-Line-Interface (CLI) in order to automate creation, deletion and upgrades of nodes.

## List all available nodes

To get a list of all nodes execute:

```bash
kubectl get nodes -o wide
```

Every node is managed by a machine resource in the `kube-system` namespace, which are bundled into machineDeployments (further explanation can be found in [cluster management API](../../02.Documentation/12.cluster-api/default.en.md)). To list all machineDeployment resources, execute:

```bash
kubectl get machineDeployments --namespace kube-system
```

## Manage sets worker nodes

When you want to change a machineDeployment you can edit the machineDeployment resource directly:

```bash
kubectl edit machineDeployment ${machineDeployment} --namespace kube-system
```

When a machineDeployment is edited the machineController will take care of updating the respective machines. Further explanation to the existing fields and update strategies can be found in [cluster management API](../../02.Documentation/12.cluster-api/default.en.md).

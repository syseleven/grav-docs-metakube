---
title: 'Cluster Management API'
published: true
taxonomy:
    tag:
        - scaling
        - kubernetes
        - cli
---

MetaKube uses the Kubernetes [Cluster Management API](https://github.com/kubernetes-sigs/cluster-api) to manage OpenStack
VMs that become nodes in the Kubernetes Cluster.

This API defines the Kubernetes resources MachineDeployment, MachineSet and Machine to manage Kubernetes nodes. These
resources behave similarly to Deployments, ReplicaSets and Pods for containers.

## Machines

If you want to create or delete a single node in your Kubernetes cluster, you can create or delete a Machine,
see this [tutorial](../../03.Tutorials/11.manage-worker-nodes-via-cli/default.en.md).

## MachineSets

A MachineSet groups one or more Machines (replicas) together and allows to easily scale this set of Machines up and down
by modifying the replica count. Usually you don't interact with MachineSets directly.

## MachineDeployments

MachineDeployments wrap a rolling update mechanism around MachineSets. This means that when you edit the machine template in a
MachineDeployment, the machine-controller will create new VMs matching the new specification and delete old VMs while ensuring
that enough nodes are in the cluster to satisfy your the update strategy defined in the MachineDeployment.

They also provide autoscaling support through the [node autoscaler](../../03.Tutorials/20.use-horizontal-node-autoscaling/default.en.md).

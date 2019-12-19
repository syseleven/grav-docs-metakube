---
title: 'Node Labels'
published: false
taxonomy:
    tag:
        - metakube
        - labels
        - pod anti-affinity
        - debugging nodes
---

Node objects in a MetaKube cluster come with a set of useful labels that can be used for debugging purposes or setting
[affinity/anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) rules while deploying applications.

In addition to the built in [node labels](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#built-in-node-labels)
MetaKube adds the following additional labels:

- **machine-controller/owned-by**

  This label contains the id of the machine object that manages the node. See [Cluster API](../12.cluster-api/default.en.md) for details.

- **machine-controller/host-id**

  This label contains the id of the physical server where the node is running on. This is particularly
  useful in scenarios where you might want to spread pod replicas across different physical servers, using pod anti-affinity rules to increase availability of your application.

- **system/cluster**

  This label contains MetaKube cluster id.

- **system/project**

  This label contains MetaKube project id.

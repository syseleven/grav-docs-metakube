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

Node objects in Metakube cluster come with a set of useful labels that can be used for debugging purposes or setting
affinity/anti-affinity rules while deploying applications

In addition to the built in [node labels](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#built-in-node-labels)
Metakube add the following additional labels:

- **machine-controller/owned-by**

  This label maps the node object to corresponding machine objects.

- **machine-controller/host-id**

  This label maps node objects to servers where the machine for that specific node is running. This is perticularly
  useful in scenarios where you might want to spread pod replicas across different physical servers, using pod anti-affinity
  rules, for better HA of your applications.

- **system/cluster**

  This label adds the metakube cluster ID to the node object.

- **system/project**

  This label adds the metakube project ID to the node object.

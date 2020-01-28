---
title: 'Node configuration and labels'
published: true
taxonomy:
    tag:
        - metakube
        - labels
        - pod anti-affinity
        - debugging nodes
---

## Node labels

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

  This label contains the MetaKube cluster id.

- **system/project**

  This label contains the MetaKube project id.

## Docker configuration

All Docker daemons that are installed with our provided images have the [Docker logging option](https://docs.docker.com/config/containers/logging/json-file/) `max-size` set to 100MB. This limits the maximum log file size to 100MB for each container created. The idea is to prevent your worker node's disks being filled up by logs created by the Docker containers. If you would like to store and keep track of your logs for a longer period of time, we recommend installing additional software to accomplish this. For example Elasticsearch, Logstash and Kibana or Loki, Promtail and Grafana.

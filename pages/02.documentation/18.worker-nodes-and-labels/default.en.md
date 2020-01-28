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

## Node-problem-detector

MetaKube deploys a [node-problem-detector](https://github.com/kubernetes/node-problem-detector) DaemonSet into every cluster.
Node-problem-detector monitors various node-related error states and adds Kubernetes [events](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#event-v1-core)
and [node conditions](https://kubernetes.io/docs/concepts/architecture/nodes/#condition) to a node if a problem occurs.
The monitored error states are things like kernel deadlocks, hung tasks and corrupted Docker images. Please note that events and
node conditions by themselves don't influence the Kubernetes scheduler, which means they won't directly prevent Kubernetes from using
the node. See the next section for one instance where a node condition is actually processed further and does prevent pods from being
scheduled on a node.

## Advanced Node Readiness Tracking

In stock Kubernetes setups, the kubelet (node daemon) does not track the availability of the pod networking (canal) or cluster DNS services on the node.
Especially during node creation, this may lead to a situation where the kubelet marks the node "ready" too early, and thus pods are scheduled onto
it that will then fail. This is particularly problematic for Job or CronJob pods.

To alleviate this problem, we're adding a customization to the [node-problem-detector](https://github.com/kubernetes/node-problem-detector) DaemonSet
that's deployed into every cluster. That customization will add a node condition with `type==MetakubeNodeReady` and status `False` to a node when the
networking or DNS aren't functional. Additional logic in MetaKube tracks this condition and adds a taint `node.syseleven.de/not-ready:NoSchedule` to
the node as long as the condition is not `True`. As a result, all nodes in MetaKube clusters will have a taint `node.syseleven.de/not-ready:NoSchedule`
when they start up, which will be removed when we determine that the network and DNS on the node are really up and running.

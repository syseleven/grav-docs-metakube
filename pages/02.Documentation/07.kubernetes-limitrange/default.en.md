---
title: 'LimitRange'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - limitrange
        - kubernetes
        - request limit
---

The `default` namespace comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/) that assigns a default CPU and memory request to pods that do not define one explicitly. We enabled this feature to try and prevent people from breaking their clusters. The default request are enforced if a container is created in the default namespace, and the container does not specify its own values for memory and CPU requests. The container is then given a default memory request of 100MiB and a default CPU request of 100Mi which is 100 millicpus. You can view the limit ranges with:

```bash
kubectl get limitranges
```

We only enforce the default requests in the default namespace. You can find additional information about requests, limits and quotas for [memory resources](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/) and [CPU resources](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/) in the kubernetes documentation.

Default LimitRange:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limit-range
  namespace: default
spec:
  limits:
  - defaultRequest:
      cpu: 100m
      memory: 100Mi
    type: Container
```

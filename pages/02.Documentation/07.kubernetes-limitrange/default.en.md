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

The `default` namespace now comes with a [LimitRange](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/) that assigns a default CPU and memory request to pods that do not define one explicitly. We enabled this feature to try and prevent people from breaking their clusters. Now if a container is created in the default namespace, and the container does not specify its own values for memory and CPU request. The container is given a default memory request of 100MiB and a default CPU request of 100Mi which is 100 millicpus. You can check view the limitranges with:

```bash
kubectl get limitranges
```

You can delete or modifiy our default settings if that is nessesary for your use case. We only inforce the default requests in the default namespace. You can find addional information about requests, limits and quotas in the kubernetes documentation.

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

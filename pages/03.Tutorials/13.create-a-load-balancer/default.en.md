---
title: 'Create a Load Balancer'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - cli
        - loadbalancer
---

[The Kubernetes manifest](#the-kubernetes-manifest)
[Configure the Load Balancer in SysEleven Stack](#configure-the-load-balancer-in-syseleven-stack)

## The Kubernetes manifest

Kubernetes has a Load Balancer type as [Service](https://kubernetes.io/docs/concepts/services-networking/service/) resource with which you can create a managed Load Balancer in SysEleven Stack for every service.
To create a kubernetes managed Load Balancer, use a manifest like the following:

```yaml
kind: Service
apiVersion: v1
metadata:
  name: $LOADBALANCER_NAME
spec:
  selector:
    $APPLICATION_LABEL: $APPLICATION_NAME
  ports:
    - protocol: TCP
      port: 80
      name: http
    - protocol: TCP
      port: 443
      name: https
  type: LoadBalancer
```

Be sure to match the [label](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) and value of the [selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) to your application and preferably choose a speaking name for the Load Balancer. The created Load Balancer might look like this

```bash
$ kubectl get svc
NAME           TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
loadbalancer   LoadBalancer   10.10.10.202   195.192.128.46   80:31228/TCP,443:30279/TCP   11s
```

This loadbalancer now exposes port 80 to the outside world and maps it to port 31228 on all cluster nodes, and it exposes port 443 to the outside world and maps it to ports 30279 on all cluster nodes. This means that a loadbalancer service is also a NodePort service (i.e. a service that exposes pods on specific "NodePorts" on all nodes).

## Configure the Load Balancer in SysEleven Stack

When binding an external IP to the Load Balancer it is by default not directly reachable from the outside. Also, it will actually not be able to reach the NodePorts of the cluster nodes (31228 and 30279 in the above example). See [Configure a Load Balancer](../14.configure-a-load-balancer/default.en.md) for a documentation how to make it accessible.

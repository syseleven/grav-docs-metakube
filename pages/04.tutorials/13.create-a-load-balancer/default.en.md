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

## The Kubernetes Manifest

Kubernetes defines [Service](https://kubernetes.io/docs/concepts/services-networking/service/) a resource type "LoadBalancer" that you can use to create a managed load balancer in SysEleven Stack for every service. To create a Kubernetes managed load balancer, use a manifest like the following:

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

Be sure to match the [label](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) and value of the [selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) to your application and preferably choose a speaking name for the Load Balancer. The created Load Balancer might look like this:

```bash
$ kubectl get svc
NAME           TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
loadbalancer   LoadBalancer   10.10.10.202   195.192.128.46   80:31228/TCP,443:30279/TCP   11s
```

This load balancer now exposes port 80 to the outside world and maps it to port 31228 on all cluster nodes, and it exposes port 443 to the outside world and maps it to ports 30279 on all cluster nodes. This means that a loadbalancer service is also a NodePort service (i.e. a service that exposes pods on specific "NodePorts" on all nodes).


## Keeping load balancer floating IPs on OpenStack

When a service of type `LoadBalancer` is created without further configuration it gets an ephemeral IP address from the OpenStack network pool of the cluster (usually `ext-net`). When the LoadBalancer is deleted the floating IP is also released. So, when a new service is created it could get the same IP but in most cases it just gets another random IP. When working with Kubernetes it can be desirable to have a more fine grained control of the used IP addresses.

!!! This has been tested with Kubernetes 1.17 and 1.18. Please keep that in mind if you run an older version (and consider upgrading).

Two options in the LoadBalancer resource come into play: The annotation `loadbalancer.openstack.org/keep-floatingip` and `.spec.loadBalancerIP`.

```yaml
kind: Service
apiVersion: v1
metadata:
  name: ...
  annotations:
    loadbalancer.openstack.org/keep-floatingip: "true"
spec:
  loadBalancerIP: "xxx.xxx.xxx.xxx"
  ...
```

- Both can be set during creation as well as for an existing service.
- If a service with the annotation is deleted the cloud-controller will not release the floating IP in OpenStack.
- `loadBalancerIP` comes with a few remarks:
  - For an existing service it should be the already assigned IP.
  - The IP needs to be already allocated (but unassigned) by your tenant, either manually or by a previous services which had the annotation set.
  - The IP has to be in the same region as the load balancer.

Among others this makes a few interesting scenarios possible:

- Delete and recreate an existing service without losing the IP. Sometimes this is for example necessary to reinstall/upgrade a helm chart.
- Move a floating IP to another cluster in the same region (and the same project). (The nodes of the new cluster need to be created before the old service is deleted, otherwise a node gets the IP assigned.)
- Making sure to always have the same IP because other systems/firewalls depend on it.

!!! If you need even more control of your used IP addresses you can also rent a dedicated IP space from us or [bring your own IP space](https://docs.syseleven.de/syseleven-stack/de/reference/network#customer-public-ip-space-bring-your-own-ip), please contact us if you are interested.

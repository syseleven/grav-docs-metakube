---
title: Load Balancer
published: true
taxonomy:
    tag:
        - loadbalancer
        - kubernetes
        - cli
---

## External Load Balancers

MetaKube clusters come with built-in support for external load balancers through OpenStack Neutron, which allows you to easily get an external IP address for a [service](https://kubernetes.io/docs/concepts/services-networking/service/). Traffic to this IP address will be automatically load balanced over all available pods of the exposed service. The [Create a Load Balancer](../../04.tutorials/13.create-a-load-balancer/default.en.md) tutorial shows you how to do this.

### Important note(s)

* Traffic sent to the load balancers will have the load balancer IP as origin. If you rely on client IPs, you currently need to pass them in another way, e.g. with a HTTP header.
* Currently, MetaKube creates no health checks when using external load balancers, therefore `service.spec.healthCheckNodePort` has no effect.
* We recommend to not set the `service.spec.externalTrafficPolicy` attribute - the default value of `Cluster` is a well tested [best practice to spread traffic evenly](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#preserving-the-client-source-ip). If you want to set it to `Local` to reduce the amount of hops in your cluster, you need to ensure that every node has a pod of your service - e.g. by using a DaemonSet. This is needed as all nodes without a pod of your service will drop the traffic received for the service.

## Ingress Controllers

In order to route traffic to applications deployed in Kubernetes it is a good practice to use an Ingress Controller which proxies incoming request to the correct services and can handle things like TLS offloading. For more information on Ingress resources and Ingress Controllers see the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/). The [Create an Ingress Controller](../../04.tutorials/15.create-an-ingress-controller/default.en.md) tutorial shows you how you can install an Ingress Controller with Let's Encrypt TLS certificate support.

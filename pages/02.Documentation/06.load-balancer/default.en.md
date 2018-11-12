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

MetaKube clusters come with built in support of external load balancers through OpenStack Neutron, which allows you to
easily get an external IP address for a [service](https://kubernetes.io/docs/concepts/services-networking/service/).

Traffic to this IP address will be automatically load balanced over all available pods of the exposed service.

The [Create a Load Balancer](../../03.Tutorials/13.create-a-load-balancer/default.en.md) tutorial shows you how to do this.

## Ingress Controllers

In order to route traffic to applications deployed in Kubernetes it is good practice to use an Ingress Controller which proxies incoming request to the correct services and can handle things like TLS offloading. For more information on Ingress resources and Ingress Controllers see the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/).

The [Create an Ingress Controller](../../03.Tutorials/15.create-an-ingress-controller/default.en.md) tutorial shows you how you can install
an Ingress Controller with Let's Encrypt TLS certificate support.

---
title: 'Setup an nginx ingress controller with the proxy protocol'
published: true
taxonomy:
    tag:
        - kubernetes
        - Ingress
        - Octavia
        - proxy protocol
---

! This tutorial only works with clusters using kubernetes version > 1.17
! Starting with kubernetes version 1.18 we are using octavia to create loadbalancers which has some new configuration options that the old one doesn't support

## Overview

You can configure the nginx ingress controller in various ways. To use the Openstack load balancer Octavia with ssl offloading you will need to configure the ingress controller with the proxy protocol. The alternative would be to use the Openstack service barbican to store your ssl certificate. Which is currently not directly supported by Kubernetes.


## Configure the nginx ingress controller via the helm values.yaml file

### values.yaml

```yaml
rbac:
  create: true

controller:
  publishService:
    enabled: true
  replicaCount: 3
  minAvailable: 1
  autoscaling:
    enabled: false
    minReplicas: 3
    maxReplicas: 6
    targetCPUUtilizationPercentage: 50
    targetMemoryUtilizationPercentage: 50
  # Octavia config
  config:
    use-forwarded-headers: "true"
    compute-full-forwarded-for: "true"
    use-proxy-protocol: "true"
  service:
    annotations:
      loadbalancer.openstack.org/proxy-protocol: "true"
```

This will deploy three replicas of the nginx ingress controller with autoscaling enabled. You will recieve an external IP address via the openstack load balancer Octavia. This uses the service type load balancer in Kubernetes. The service will include additional annotations for the proxy protocol. Which will ensure that the original client IP address is inserted into the HTTP header. So that the backend service is able to get the real source IP of the request. Please check the version of the nginx ingress contoller that you are deploying. The version in this example maybe outdated.

### Deployment

```shell
# Create a namespace to deploy the nginx ingress controller into
kubectl create namespace ingress-nginx

# Add the ingress contrller helm repository if it is not added.
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Deploy the nginx ingress controller
helm upgrade --install -f values.yaml --namespace ingress-nginx ingress-nginx ingress-nginx/ingress-nginx --version 3.13.0
```

## Additional configuration options. Preserve the Load balancers external IP address (floating IP)


The example below configures the nginx ingress controller after the first update for example. You will need to add the external IP address you recieved after the installation to the service. You will also need to add an additional annotation to the service to keep the external IP address (floating IP).

### Additional yaml to add to the nginx ingress controller (optional)

```yaml
  service:
    enabled: true
    loadBalancerIP: "198.51.100.10"

    annotations:
      loadbalancer.openstack.org/keep-floatingip: true

    type: LoadBalancer
```

## Documentation with further configuration options

The openstack external cloud provider has a list of all supported service annotations you may use. You can find the documentation here:

[Exposing applications using services of LoadBalancer type](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/expose-applications-using-loadbalancer-type-service.md)

For more informations about how to configure a loadbalancer service you can read this tutorial:

[Create a Load Balancer](https://docs.syseleven.de/metakube/en/tutorials/create-a-load-balancer)

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

## Overview

You can configure the Nginx Ingress controller in various ways. To use the Openstack load balancer Octavia with ssl offloading you will need to configure the ingress controller with the proxy protocol. The alternative would be to use the Openstack service barbican to store your ssl certificate. Which is currently not directly supported by Kubernetes.


## Configure the nginx ingress controller via the helm values.yaml file

### values.yaml

```yaml
## nginx configuration
##
controller:
  name: controller
  image:
    registry: eu.gcr.io
    repository: k8s-artifacts-prod/ingress-nginx/controller
    tag: "v0.34.1"
    pullPolicy: IfNotPresent
    # www-data -> uid 101
    runAsUser: 101
    allowPrivilegeEscalation: true

  # Configures the ports the nginx-controller listens on
  containerPort:
    http: 80
    https: 443

  config:
    use-forwarded-headers: "true"
    compute-full-forwarded-for: "true"
    use-proxy-protocol: "true"

  ## Allows customization of the external service
  ## the ingress will be bound to via DNS
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

  service:
    enabled: true

    annotations:
      loadbalancer.openstack.org/proxy-protocol: true

    enableHttp: true
    enableHttps: true

    ports:
      https: 443

    targetPorts:
      http: http
      https: https

    type: LoadBalancer
```

This will deploy three replicas of the nginx ingress controller with autoscaling enabled. You will recieve an external IP address via the openstack load balancer. This uses the service type load balancer in Kubernetes. The service will include additional annotations for the proxy protocol. Which will ensure that the original client IP address is inserted into the HTTP header. So that the backend service is able to get the real source IP of the request. Please check the version of the nginx ingress contoller that you are deploying. The version in the example maybe outdated.

### Deployment

```shell
# Create a namespace to deploy the nginx ingress controller into
kubectl create namespace nginx-ingress

# Deploy the nginx ingress controller
helm upgrade --install -f values.yaml --namespace nginx-ingress nginx-ingress stable/nginx-ingress
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

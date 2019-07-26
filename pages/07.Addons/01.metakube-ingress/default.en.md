---
title: 'Ingress Controller'
published: true
date: '18-07-2019 13:00'
taxonomy:
    tag:
        - metakube
        - ingress
        - nginx
---

With the NGINX Kubernetes Ingress controller, you get basic load balancing and SSL/TLS termination for all Kubernetes Ingress resources.

You can find more information on ingresses and the nginx ingress controller and all it's configuration options at:

* the [Kubernetes Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* the [Nginx Ingress Controller Documentation](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/)
* the tutorial [Deploy an Application](../../03.Tutorials/16.deploy-an-application/default.en.md)

## Installation

For information on how to install and uninstall an addon see [Addons](../default.en.md).

## Customization

The following customization options are possible:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 64Mi |
| CPU Limits | What is the maximum of used milli CPUs | 100m |
| Memory Requests | What is the maximum of used memory | 64Mi |
| Replicas | How many replicas should be running | 2 |
| Use Auto Scaling | Should a HorizontalPodAutoscaler be created | false |
| Min Replicas | What should be the minimum amount of replicas in the AutoScaler | 2 |
| Max Replicas | What should be the maximum amount of replicas in the AutoScaler | 4 |

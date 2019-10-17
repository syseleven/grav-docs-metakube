---
title: 'Ingress Controller'
published: true
date: '18-07-2019 13:00'
taxonomy:
    tag:
        - metakube
        - ingress
        - nginx
        - addon
---

With the NGINX Kubernetes Ingress controller, you get basic load balancing and SSL/TLS termination for all Kubernetes Ingress resources.

You can find more information on ingresses and the nginx ingress controller and all of it's configuration options at:

* the [Kubernetes Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* the [Nginx Ingress Controller Documentation](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/)
* the tutorial [Deploy an Application](../../04.tutorials/16.deploy-an-application/default.en.md)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 128Mi |
| CPU Limits | What is the maximum of used milli CPUs | 200m |
| Memory Limits | What is the maximum of used memory | 512Mi |
| Replicas | How many replicas should be running | 2 |
| Use Auto Scaling | Should a HorizontalPodAutoscaler be created | false |
| Min Replicas | The minimum amount of replicas in the AutoScaler | 2 |
| Max Replicas | The maximum amount of replicas in the AutoScaler | 4 |
| Default TLS certificate | Path to a secret in the form `namespace/secret-name` that contains a tls certificate that should be used if an Ingress does not define one | |

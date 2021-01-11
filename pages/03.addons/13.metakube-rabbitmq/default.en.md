---
title: 'RabbitMQ'
published: true
taxonomy:
    tag:
        - metakube
        - addon
        - rabbitmq
---

[RabbitMQ](https://www.rabbitmq.com/) is the most widely deployed open source message broker.

You can find more information at [RabbitMQ documentation page](https://www.rabbitmq.com/documentation.html).

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| CPU Requests | How many milli CPUs should be reserved | 200m |
| Memory Requests | How much memory should be reserved | 256Mi |
| CPU Limits | What is the maximum of used milli CPUs | 1000m |
| Memory Limits | What is the maximum of used memory | 1Gi |
| Replicas | How many replicas should be running | 2 |

## Getting RabbitMQ Credentials

After the installation of the Add-On you can retrieve the RabbitMQ credentials with the following command:

```bash
echo "Username: user" && echo "Password: $(kubectl get secret -n syseleven-rabbitmq syseleven-rabbitmq -o jsonpath="{.data.rabbitmq-password}"|base64 --decode)"
```

## Accessing the RabbitMQ Management UI

You can reach the RabbitMQ Management UI from the cluster detail page. In addition to the standard SysEleven authentication, you have to log into RabbitMQ Management UI itself using your credentials.

## Accessing RabbitMQ from within Kubernetes

When installing RabbitMQ corresponding Kubernetes services are created. You can use Kubernetes mechanisms to reach to Add-On Pods using the following services:

```bash
Name                        Namespace
syseleven-rabbitmq          syseleven-rabbitmq
syseleven-rabbitmq-headless syseleven-rabbitmq
```

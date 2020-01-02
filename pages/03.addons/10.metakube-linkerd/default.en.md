---
title: 'Linkerd'
published: true
date: '14-10-2019 19:00'
taxonomy:
    tag:
        - service mesh
        - linkerd
        - addon
---

The Linkerd Add-On installs [Linkerd](https://linkerd.io/), an ultralight service mesh for Kubernetes. It gives you observability, reliability, and security without requiring any code changes.

For more information on usage, features and design of Linkerd refer to upstream [documentation](https://linkerd.io/2/overview/)

## Installation

For information on how to install and uninstall an Add-On see [Add-Ons](../default.en.md).

## Resource Requests

Linkerd Add-on requests at least 5 CPUs and 2 Gi of memory from your Metakube cluster. Please make sure enough resources are available
in your cluster while installing the Linkerd Add-on

## Customization

The following customization options are possible:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| High availability | Installs Linkerd in highly-available mode (recommended for production) | true |

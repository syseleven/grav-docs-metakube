---
title: 'How to use the proxy protocol'
published: true
date: '27-08-2020 14:26'
taxonomy:
    tag:
        - metakube
        - cluster
        - octavia
        - proxy protocol
        - client ip
---

## What is the Proxy Protocol?

The proxy protocol is an industry standard to pass client connection information through a load balancer on to the destination server. Activating the proxy protocol allows you to see the original client ip address in your application logs.

## Configuring nginx to use the proxy protocol

There are multiple webservers and ways of configuring the proxy protocol. We also provide a tutorial on how to do this with the nginx ingress controller here [Setup and nginx ingress controller with the proxy protocol](../27.setup-an-nginx-ingress-controller-with-the-proxy-protocol/default.en.md). This tutorial will show you how to configure the proxy protocol with nginx. For further information please refer to [Accepting the proxy protocol](https://docs.nginx.com/nginx/admin-guide/load-balancer/using-proxy-protocol/). In general nginx will require an http or a stream block to activate the proxy protocol. This block can only be set once. So it often makes sense to configure your domain (vhost) via the main nginx config. Because the http block is normally set there.

``` bash
```

## Upload to SysEleven Stack via CLI

Alternatively to the upload via GUI, you can upload a new image to the SysEleven Stack via CLI. You need to have the `python-openstackclient` installed, which includes the CLI tool `glance`. You can simply upload it to the cloud with the following command:

``` bash
glance image-create \
  --name <imageName> \
  --container-format bare \
  --disk-format qcow2 \
  --file <path/to/image>
```

## Choose in cluster creation

In the [cluster creation process](../02.create-a-cluster/default.en.md) you will be asked which image and operating system you want to use. Choose the correct OS and enter the name you chose for your image before. The cluster will then be created with your custom image:

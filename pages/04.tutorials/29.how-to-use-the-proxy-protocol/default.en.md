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

There are multiple webservers and ways of configuring the proxy protocol. We also provide a tutorial on how to do this with the nginx ingress controller here [Setup and nginx ingress controller with the proxy protocol](../27.setup-an-nginx-ingress-controller-with-the-proxy-protocol/default.en.md). This tutorial will show you how to configure the proxy protocol with nginx. For further information please refer to [Accepting the proxy protocol](https://docs.nginx.com/nginx/admin-guide/load-balancer/using-proxy-protocol/). In general nginx will require an http or a stream block to activate the proxy protocol. This block can only be set once. So it often makes sense to configure your domain (vhost) via the main nginx config. Because the http block is normally set there. This is a minimal example on how to accomplish this

``` bash
http {
    server {
        listen 8080 proxy_protocol;

        root /usr/share/nginx/html;
        try_files /index.html =404;

        proxy_set_header X-Real-IP              $remote_addr;

        # Pass the original X-Forwarded-For
        proxy_set_header X-Original-Forwarded-For $http_x_forwarded_for;

        # mitigate HTTPoxy Vulnerability
        # https://www.nginx.com/blog/mitigating-the-httpoxy-vulnerability-with-nginx/
        proxy_set_header Proxy                  "";
        }
}
```

## Configuring other webservers

It is also possible to configure other webserver to use the proxy protocol

If you would like to use the apache webserver you may use the apache module remoteip. Instructions on how to configure the remote ip module can be found here:

[apache module remote ip](https://httpd.apache.org/docs/2.4/mod/mod_remoteip.html)

## Configuring you kubernetes service endpoint for the proxy protocol

The openstack load balancer octavia can be configured via kubernetes annotations. To configure a service endpoint to use the proxy protocol you may use the following yaml

``` bash
apiVersion: v1
kind: Service
metadata:
  annotations
:
    loadbalancer.openstack.org/proxy-protocol: "true"
  name: webapp-svc
  labels:
    app: webapp-svc
spec:
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: webapp
  type: LoadBalancer
```

## Further information

[Exposing applications using services of LoadBalancer type](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/expose-applications-using-loadbalancer-type-service.md#exposing-applications-using-services-of-loadbalancer-type)

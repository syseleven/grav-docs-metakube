---
title: 'Deliver assets from SysEleven Object Storage'
published: true
taxonomy:
    tag:
        - kubernetes
        - Object storage
        - S3
        - assets
        - Ingress
---

## Overview

This tutorial will show you how to deliver assets from the S3 compatible SysEleven Object Storage with ingress-nginx. As the SysEleven Object Storage does not support CORS headers at this time, this is necessary to avoid CORS errors.

For more detailed information about CORS, check out [Mozilla’s great documentation](https://developer.mozilla.org/de/docs/Web/HTTP/CORS).

You will need:

* A MetaKube Cluster with [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/) installed
* A SysEleven Object Storage bucket, see [the reference](https://docs.syseleven.de/syseleven-stack/en/reference/object-storage) for details
* (optional) A deployed application

For this configuration to work, your assets have to be served at one or more distinct paths, e.g. `/assets` or `/img` as all requests to those paths will be served from the S3 bucket.

## Create a Service for the S3 bucket

To use the S3 bucket as upstream for the ingress-nginx, we need to make it available as a Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: assets
spec:
  type: ExternalName
  externalName: $BUCKET.s3.$REGION.cloud.syseleven.net
```

Replace

* `$BUCKET` with the name of your bucket, e.g. `assets-test`
* `$REGION` with the region where your bucket is located (e.g. `dbl`)

## Create an ingress for the asset paths

Then we only need to add an Ingress resource for the paths that the assets have:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: HTTPS
    nginx.ingress.kubernetes.io/upstream-vhost: $BUCKET.s3.$REGION.cloud.syseleven.net
  name: assets-ingress
spec:
  # No TLS config needed, only one ingress for a hostname needs to have the TLS config, see https://github.com/kubernetes/ingress-nginx/issues/4106#issuecomment-494305090
  rules:
  - host: $APPLICATION_HOSTNAME
    http:
      paths:
      - path: /img/
        backend:
          serviceName: assets
          servicePort: 443
      - path: /css/
        backend:
          serviceName: assets
          servicePort: 443
```

Replace

* as above: `$BUCKET` with the name of your bucket, e.g. `assets-test`
* as above: `$REGION` with the region where your bucket is located (e.g. `dbl`)
* `$APPLICATION_HOSTNAME` with the host name your application is served at

and add as many elements to `path` as you need.

Once you’ve applied both resources, all requests to the specified paths for your hostname will be served from the bucket, all other requests will still go to your application.

Please note that for this to work, the asset paths in the Ingress above must always be the most specific paths. If the Ingress resource of your application has e.g. configuration for the path `/img/backend`, requests to this path will still go to your application.

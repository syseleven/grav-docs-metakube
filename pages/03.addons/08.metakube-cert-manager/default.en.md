---
title: 'Cert Manager'
published: true
taxonomy:
    tag:
        - metakube
        - addon
        - jetstack
        - certmanager
        - cert-manager
        - certificates
---

The Jetstack cert-manager seamlessly integrates with the [NGINX Ingress](../01.metakube-ingress/default.en.md) to automatically create and extend SSL/TLS certificates for your Ingress.

You can find more information on

* the [cert-manager docs](https://docs.cert-manager.io/en/latest/)
* the [github repository](https://github.com/jetstack/cert-manager)
* the [ingress controller tutorial](../../04.tutorials/15.create-an-ingress-controller/default.en.md)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

When installing on Openstack the required Webhook to use [Openstack DNS](https://docs.syseleven.de/syseleven-stack/en/reference/dns) will be deployed automatically in the cluster.

## Customization

There are no customization options.

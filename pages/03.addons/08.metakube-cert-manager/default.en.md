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
        - lets encrypt
---

The Jetstack cert-manager allows to automatically create and extend SSL/TLS certificates with [Let's Encrypt](https://letsencrypt.org). It also seamlessly integrates with [Ingress Controllers](../01.metakube-ingress/default.en.md) to automatically create certificates for [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) resources.

You can find more information on

* the [cert-manager docs](https://docs.cert-manager.io/en/release-0.10/)
* the [github repository](https://github.com/jetstack/cert-manager)
* the [ingress controller tutorial](../../04.tutorials/15.create-an-ingress-controller/default.en.md)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

When installing on Openstack the required Webhook to use [Openstack DNS](https://docs.syseleven.de/syseleven-stack/en/reference/dns) will be deployed automatically in the cluster.

## Customization

There are no customization options.

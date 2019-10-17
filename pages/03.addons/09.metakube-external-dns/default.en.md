---
title: 'ExternalDNS'
published: true
taxonomy:
    tag:
        - metakube
        - addon
        - jetstack
        - ExternalDNS
        - DNS
---

External-dns integrates with [OpenStack Designate](https://docs.syseleven.de/syseleven-stack/reference/dns), [AWS Route53](https://aws.amazon.com/route53), [Cloudflare](https://www.cloudflare.com/dns/) and many more DNS providers to synchronize the external IPs of exposed Kubernetes [Services](https://kubernetes.io/docs/concepts/services-networking/service/) and [Ingresses](https://kubernetes.io/docs/concepts/services-networking/ingress/) with these public DNS servers.

In [Deploy Application](../../04.tutorials/16.deploy-an-application/default.en.md) you can see how you can add and external-dns annotation to your kubernetes services to register the corresponding hostname with your public DNS provider.

You can find more information on

* the [github repository](https://github.com/kubernetes-incubator/external-dns)
* the [external-dns tutorials](https://github.com/kubernetes-incubator/external-dns/blob/master/docs/tutorials)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| Domain filter | A filter for domains where DNS entries should be created for | |
| DNS Provider | Public DNS provider to use. | designate |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 128Mi |
| CPU Limits | What is the maximum of used milli CPUs | 200m |
| Memory Limits | What is the maximum of used memory | 512Mi |

!!! Depending on the selected DNS Provider further credential options will required to be filled in


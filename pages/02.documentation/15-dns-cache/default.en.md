---
title: 'MetaKube DNS Cache'
taxonomy:
    tag:
        - metakube
        - dns
        - dns-cache
        - nodelocal-dns-cache
---

This addon runs a node-local-dns pod on all cluster nodes. The pod runs CoreDNS as the dns cache. It runs with hostNetwork:True and creates a dedicated dummy interface with a link local ip(169.254.20.10/32 by default) to listen for DNS queries. The cache instances connect to clusterDNS in case of cache misses. We provide the node-local-dns cache as a default addon to prevent dns timeouts within the cluster. Due to the way kubernetes works the internal dns resolution is a critical part of the cluster. 

For more information also have a look at the [Nodelocal DNS Cache GitHub Page](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/dns/nodelocaldns).


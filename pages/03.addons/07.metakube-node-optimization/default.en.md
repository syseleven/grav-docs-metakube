---
title: 'Experimental Node Optimizations'
published: true
taxonomy:
    tag:
        - metakube
        - optimization
        - kernel
        - addon
---

! Usage of this addon is currently highly experimental, use at your own risk.

The node optimization addon reconfigures various Linux kernel settings on every node to improve cluster performance, especially in larger clusters.

## Optimized Kernel Settings

```ini
net.ipv4.ip_local_port_range="1025 65535"
net.ipv4.tcp_tw_reuse=1
net.ipv4.tcp_fin_timeout=15
net.core.somaxconn=4096
net.core.netdev_max_backlog=4096
net.core.rmem_max=16777216
net.core.wmem_max=16777216
net.ipv4.tcp_max_syn_backlog=20480
net.ipv4.tcp_max_tw_buckets=400000
net.ipv4.tcp_no_metrics_save=1
net.ipv4.tcp_rmem="4096 87380 16777216"
net.ipv4.tcp_syn_retries=2
net.ipv4.tcp_synack_retries=2
net.ipv4.tcp_wmem="4096 65536 16777216"
net.netfilter.nf_conntrack_max=262144
net.netfilter.nf_conntrack_tcp_timeout_established=86400
net.ipv4.neigh.default.gc_thresh1=8096
net.ipv4.neigh.default.gc_thresh2=12288
net.ipv4.neigh.default.gc_thresh3=16384
```

## Installation

For information on how to install and uninstall an addon see [MetaKube Addons](../default.en.md).

## Customization

There are no customization options.

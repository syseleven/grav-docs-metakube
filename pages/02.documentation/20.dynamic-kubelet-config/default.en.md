---
title: 'Dynamic kubelet config'
published: true
taxonomy:
    tag:
        - metakube
        - kubelet config
        - dynamic kubelet
---

## Dynamic kubelet config

It is possible to dynamically change the kubelet settings for a node deployment in metakube.
This feature can be used to change the kubelet settings on already running nodes.

### Enabling the dynamic kubelet config option

The option can be toggled while creating a new node deployment or when editing an existing one.

![Enable dynamic kubelet config](kubelet-config-toggle.png)

### Customizing the kubelet config

! misconfiguring the kubelet config can possibly prevent the kubelet from starting up.

When this option is enabled multiple configmaps for all supported kubernetes versions will be created in the `kube-system` namespace.

All nodes are watching for changes in the configmap with the same kubernetes version that is running on the node.
Please keep this in mind because nodes will be using a different configmap after upgrading to a newer kubernetes minor version (e.g. 1.17 -> 1.18).

Once a change is noticed `systemd` will restart the kubelet so that it can use the new configuration.

All customizable kubelet configuration can be found in the [Kubernetes Docs](https://godoc.org/k8s.io/kubelet/config/v1beta1#KubeletConfiguration).
The config keys that are being used in the kubelet configuration are the names after the `json:` prefix.

To get a more in depth look into how the kubelet gets reconfigured you can look at [this kubernetes guide](https://kubernetes.io/docs/tasks/administer-cluster/reconfigure-kubelet/).
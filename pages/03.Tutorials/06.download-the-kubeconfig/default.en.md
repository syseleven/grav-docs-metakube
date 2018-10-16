---
title: 'Download the kubeconfig'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - gui
---

To download the kubeconfig, navigate to `Manage Cluster` and select the correct cluster. On the top right you can find a button `Download config`.

![Download config button in the top right corner](image_download-kubeconfig_01.png)

The token in the kubeconfig gives you full admin rights within the Kubernetes cluster.

You can change the admin token by revoking the existing one on the cluster detail page. Already downloaded kubeconfigs are not
valid anymore then and need to be re-downloaded.

![Revoke the admin token](revoke-admin-token.png)

---
title: 'MetaKube Web Terminal'
published: true
taxonomy:
    tag:
        - kubernetes
        - gui
        - webterminal
---

Every MetaKube cluster comes with a web-terminal in the MetaKube web interface. The web-terminal gives you command line access to your cluster and your openstack resources directly from the browser.
To access the web-terminal just click on the `Web Terminal` link on the cluster detail page:

![MetaKube Dashboard](metakube-dashboard.png)

This bash terminal runs on Alpine Linux in the background and comes installed with all the necessary tools to interact with your cluster and your openstack resources:

1) [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
2) [helm](https://helm.sh/)
3) [tiller](https://docs.helm.sh/install/#installing-tiller)
4) [Velero](https://github.com/heptio/velero)
5) [OpenStack CLI Tools](https://docs.syseleven.de/syseleven-stack/en/howtos/openstack-cli)
6) curl, wget and git

![MetaKube Webterminal](metakube-webterminal.png)

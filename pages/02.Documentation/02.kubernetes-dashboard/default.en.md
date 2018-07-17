---
title: 'Kubernetes Dashboard'
media_order: 'kubernetes-dashboard.png,kubernetes-dashboard-login.png'
published: true
date: '17-07-2018 16:47'
publish_date: '01-08-2018 12:00'
taxonomy:
    tag:
        - kubernetes
        - gui
---

For convenience we install the [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) into every cluster.
Though for security reasons it is not exposed publicly. In order to access it, [install kubectl](./using-kubectl.md) and run:

```bash
kubectl proxy
```

And then go to:

[`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

![](kubernetes-dashboard-login.png)

Use the downloaded kubeconfig to log in.

![](kubernetes-dashboard.png)
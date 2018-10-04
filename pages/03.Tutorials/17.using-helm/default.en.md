---
title: 'Using Helm'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - cli
        - helm
---

Helm is a package manager for Kubernetes which makes it easy to install common applications like an [Ingress Controller](../15.create-an-ingress-controller/default.en.md) into your Kubernetes cluster.

## Installation

All MetaKube clusters come pre-configured with Helm out of the box.
You just have to install the helm command line client to start deploying Helm charts.

See the official Helm [installation guide](https://docs.helm.sh/using_helm/#installing-the-helm-client) for instructions for your operation system.

## Usage

After you installed the command line client you can run

```bash
helm ls
```

to get a list of all installed charts, which initially is empty.

You can find a list of publicly available Helm charts at [Kubeapps Hub](https://hub.kubeapps.com/).

To install for example wordpress, just run

```bash
helm install stable/wordpress --namespace default --name my-wordpress
```

This will deploy WordPress, MariaDB and configure a [LoadBalancer](../13.create-a-load-balancer/default.en.md).

It may take a few minutes for the LoadBalancer IP and pods to be available.
You can watch the status of the service until an external IP appears with

```bash
kubectl get svc --namespace default -w -l release=my-wordpress
```

You can watch the status of all pods until they are ready and running with

```bash
kubectl get pods --namespace default -w -l release=my-wordpress
```

To get the WordPress URL then run

```bash
export SERVICE_IP=$(kubectl get svc --namespace default my-wordpress-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo "WordPress URL: http://$SERVICE_IP/"
echo "WordPress Admin URL: http://$SERVICE_IP/admin"
```

And to get the user credentials

```bash
echo Username: user
echo Password: $(kubectl get secret --namespace default my-wordpress-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```

To delete WordPress again, you can run:

```bash
helm del --purge my-wordpress
```

For more information on how to use helm have a look at the [documentation](https://docs.helm.sh/using_helm/).

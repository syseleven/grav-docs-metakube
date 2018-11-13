---
title: 'Create an Ingress Controller'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - ingress
        - loadbalacer
        - cli
---

In order to route traffic to applications deployed in Kubernetes it is good practice to use an Ingress Controller which proxies incoming request to the correct services and can handle things like TLS offloading. For more information on Ingress resources and Ingress Controllers see the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/).

## NGINX Ingress Controller

[Nginx Ingress Controller Installation](#nginx-ingress-controller-installation)

A popular ingress controller is the [nginx ingress controller](https://kubernetes.github.io/ingress-nginx/).

### NGINX Ingress Controller Installation

The easiest way to install it in your cluster is through [Helm](../17.using-helm/default.en.md). When Helm is ready to be used, run

```bash
helm install stable/nginx-ingress --name nginx-ingress --namespace kube-system  --set "rbac.create=true"
```

to install the NGINX Ingress Controller in the cluster. This will automatically create a [Type Load Balancer service](../13.create-a-load-balancer/default.en.md) for you.

## Cert-Manager

[Cert-Manager Installation](#cert-manager-installation)
[Configure cluster issuer](#configure-cluster-issuer)

If you want to use [Let's Encrypt](https://letsencrypt.org/) to automatically manage TLS certificates for your ingress resources, you also have to install [cert-manager](https://cert-manager.readthedocs.io/en/latest/).

### Cert-Manager Installation

This can be done through [Helm](../17.using-helm/default.en.md) as well

```bash
helm install --name cert-manager --namespace kube-system stable/cert-manager
```

### Configure cluster issuer

After installing the cert-manager you have to configure how it can fetch certificates. For that you have to add a _ClusterIssuer_ to your Kubernetes cluster

```bash
cat <<'EOF' | kubectl apply -f -
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    # The ACME server URL
    server: https://acme-v01.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: your-email@example.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-prod
    # Enable HTTP01 validations
    http01: {}
EOF
```

In [Deploy Application](../16.deploy-an-application/default.en.md) you can see how you can use this issuer to fetch a certificate.

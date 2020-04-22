---
title: 'Setup ingress auth to use keycloak oauth'
published: true
taxonomy:
    tag:
        - kubernetes
        - Keycloak
        - SSO
        - OAuth
        - Ingress
---

## Overview

You can configure any ingress object to use keycloak as auth backend. This can be a substitute for auth basic or any other frontend you want proteted by an auth mechanism.

For this you need to deploy an **oauth-proxy** and configure your **ingress object(s)**.

## Prerequisites

Before you can configure this you need to set a **client_id** (and get the **client_secret**) in your **keycloak realm**. You can find more about this in the [keycloak tutorial](../06.external-authentication/default.en.md)

## Configure oauth-proxy

### values.yaml

```yaml
# https://github.com/oauth2-proxy/oauth2-proxy/blob/7c3efe4f42bc37ccab613fe5002c172e147e3195/docs/2_auth.md#keycloak-auth-provider
extraArgs:
  provider: keycloak
  provider-display-name: SysEleven Login
  client-id: XXX # please fill in your client_id
  client-secret: XXX # please fill in your client_secret
  login-url: "https://login.syseleven.de/auth/realms/syseleven/protocol/openid-connect/auth"
  redeem-url: "https://login.syseleven.de/auth/realms/syseleven/protocol/openid-connect/token"
  validate-url: "https://login.syseleven.de/auth/realms/syseleven/protocol/openid-connect/userinfo"
  keycloak-group: XXX # please fill in your group that shall have access to it

replicaCount: 2
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: oauth2-proxy
        topologyKey: "kubernetes.io/hostname"
resources:
  limits:
    cpu: 200m
    memory: 100Mi
  requests:
    cpu: 100m
    memory: 25Mi
```

### Makefile

```shell
OAUTH_CHART_VERSION=2.4.1
[...]
test:
    helm diff -C5 --allow-unreleased upgrade --namespace MYNAMESPACE -f values.yaml oauth2-proxy stable/oauth2-proxy --version ${OAUTH_CHART_VERSION}
deploy:
    helm upgrade -i --namespace MYNAMESPACE -f values.yaml oauth2-proxy stable/oauth2-proxy --version ${OAUTH_CHART_VERSION}
```

```shell
# See what would be deployed
make test

# Deploy
make deploy
```

## Configure ingress

Setup the original ingress object to use **nginx.ingress.kubernetes.io/auth*** to point to the */oauth2* path. This path needs to be defined in a seperate ingress object (because this one does not have auth configured for itself).

The second ingress objects defines the */oauth2* path under the same domain and points to the oauth2-proxy deployed aboved.

The example below configures the domain auth.mydomain.de with the nginx ingress using a [letsencrypt certificate](../15.create-an-ingress-controller/default.en.md) with an alertmanager service as backend.

### Original ingress

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    certmanager.k8s.io/cluster-issuer: designate-clusterissuer-prod
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/auth-url: "https://$host/oauth2/auth"
    nginx.ingress.kubernetes.io/auth-signin: "https://$host/oauth2/start?rd=$escaped_request_uri"
  name: external-auth-oauth2
  namespace: MYNAMESPACE
spec:
  rules:
  - host: auth.mydomain.de
    http:
      paths:
      - backend:
          serviceName: alertmanager-operated
          servicePort: 9093
        path: /
  tls:
  - hosts:
    - auth.mydomain.de
    secretName: auth.mydomain.de
```

### Additional /oauth2 ingress

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: oauth2-proxy
  namespace: monitoring
  annotations:
    certmanager.k8s.io/cluster-issuer: designate-clusterissuer-prod
    kubernetes.io/ingress.class: nginx
spec:
  rules:
  - host: auth.mydomain.de
    http:
      paths:
      - backend:
          serviceName: oauth2-proxy
          servicePort: 80
        path: /oauth2
  tls:
  - hosts:
    - auth.mydomain.de
    secretName: auth.mydomain.de
```

In case you are using NetworkPolicies you need to open TCP/4180 (and in this case TCP/9093 for the alertmanager backend) for the ingress controller to the oauth-proxy pods.

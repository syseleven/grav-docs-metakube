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

You can configure any ingress object to use keycloak as auth backend. This can be a substitute for auth basic or any other frontend you want protected by an auth mechanism.

For this you need to deploy an **oauth-proxy** and configure your **ingress object(s)**.

## Prerequisites

Before you can configure this you need a **client_id** (and the **client_secret**) from your **keycloak realm**. You can find more about this in the [keycloak tutorial](../06.external-authentication/default.en.md).

## Configure oauth-proxy

### values.yaml

```yaml
# https://github.com/oauth2-proxy/oauth2-proxy/blob/7c3efe4f42bc37ccab613fe5002c172e147e3195/docs/2_auth.md#keycloak-auth-provider
extraArgs:
  provider: keycloak
  set-xauthrequest: true
  provider-display-name: SysEleven Login
  client-id: XXX # Change XXX to your client_id
  client-secret: XXX # Change XXX to your client_secret
  login-url: "https://login.syseleven.de/auth/realms/REALM_NAME/protocol/openid-connect/auth" # Change REALM_NAME to your realm
  redeem-url: "https://login.syseleven.de/auth/realms/REALM_NAME/protocol/openid-connect/token" # Change REALM_NAME to your realm
  validate-url: "https://login.syseleven.de/auth/realms/REALM_NAME/protocol/openid-connect/userinfo" # Change REALM_NAME to your realm
  keycloak-group: XXX # Change XXX by your group that shall have access to it

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

### Deployment

```shell
OAUTH_CHART_VERSION=3.2.2

# See what would be deployed
helm diff -C5 --allow-unreleased upgrade --namespace MYNAMESPACE -f values.yaml oauth2-proxy stable/oauth2-proxy --version "${OAUTH_CHART_VERSION}"

# Deploy oauth-proxy
helm upgrade -i --namespace MYNAMESPACE -f values.yaml oauth2-proxy stable/oauth2-proxy --version "${OAUTH_CHART_VERSION}"
```

## Configure ingress

Setup the original ingress object to use **nginx.ingress.kubernetes.io/auth** to point to the */oauth2* path. This path needs to be defined in a seperate ingress object (because this one does not have auth configured for itself).

The second ingress objects defines the */oauth2* path under the same domain and points to the oauth2-proxy deployed aboved.

The example below configures the domain auth.mydomain.de with the nginx ingress using a [letsencrypt certificate](../15.create-an-ingress-controller/default.en.md) with an alertmanager service as backend.

### Original ingress

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    cert-manager.io/cluster-issuer: designate-clusterissuer-prod
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/auth-url: "https://$host/oauth2/auth"
    nginx.ingress.kubernetes.io/auth-signin: "https://$host/oauth2/start?rd=$escaped_request_uri"
    nginx.ingress.kubernetes.io/auth-response-headers: "x-auth-request-user, x-auth-request-email"
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
  namespace: MYNAMESPACE
  annotations:
    cert-manager.io/cluster-issuer: designate-clusterissuer-prod
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

## Get user header in application

By using the `set-xauthrequest: true` option in the oauth2-proxy and the `nginx.ingress.kubernetes.io/auth-response-headers: "x-auth-request-user, x-auth-request-email"` annotation in the nginx-ingress you will get the Email address of the user.

An example request from the ingress-controller to the application behind the oauth2-proxy will look like this:

```http
GET / HTTP/1.1
x-auth-request-user: exampleuser@exampledomain.tld
x-auth-request-email: exampleuser@exampledomain.tld
Host: exampledomain.tld
X-Request-ID: 2b05317a51af4bac53c0419bd30bd188
X-Real-IP: 172.25.14.0
X-Forwarded-For: 172.25.14.0
X-Forwarded-Host: exampledomain.tld
X-Forwarded-Port: 443
X-Forwarded-Proto: https
X-Scheme: https
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:79.0) Gecko/20100101 Firefox/79.0
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
accept-language: en-GB,en;q=0.5
accept-encoding: gzip, deflate, br
upgrade-insecure-requests: 1
if-none-match: W/"11111-iVaVGenrMCRZluT1IB2n2xq/3jI"
cache-control: max-age=0
cookie: _ga=GA1.2.619497248.1570693283; _oauth2_proxy=$TOKEN
```

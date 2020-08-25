---
title: 'Setup JWT authentication with keycloak gatekeeper'
published: true
taxonomy:
    tag:
        - kubernetes
        - Keycloak
        - Keycloak-gatekeeper
        - JWT
---

## Overview

For applications that are API-only, setting up authentication with JWT tokens is a reasonable choice, as
the standard OpenID authentication flow might not be available for exemple CLI applications, for example.

Also, if you're using `nginx-ingress` controller, this tutorial can provide an alternative
method to add keycloak authentication since it is not supported out of the box.

For this you need to deploy an **keycloak-gatekeeper sidecar** and configure your **deployment object(s)**.

## Prerequisites

First of all you need one **client_id** (and the **client_secret**) from your **keycloak realm** to be
used by **gatekeeper**. You can find more about this in the [keycloak tutorial](../06.external-authentication/default.en.md).
**Access type** should be `confidential`. To make easier to follow let's say your
**client_id** here is `gatekeeper`.

Then, you need another pair of **client_id** and **client_secret** to be used by your API client.
This one will have the following differences from the first one when adding the Client:

- **client_id** here can be anything, like `my_client_app`
- On **Advanced settings** it is advisable to increase the **Access token lifespan** from 10-30 minutes,
  so that you don't need to issue a new token as frequently.
- At **Authentication flow overrides** set **Browser flow** to `Browser` and **Direct Grant Flow** `Direct grant`

Recent keycloak versions no longer adds the **client_id** to the audience field `aud` in the **access token**. That said we need create and configure a [Client Scope](https://www.keycloak.org/docs/latest/server_admin/#_client_scopes):

1. After clicking the **Client Scopes** menu on the sidebar, create one. (for ex: `api`)
2. Then within `api` settings page, go to **Mappers** tab and create one with the following configuration:
    - **Name** can be anything, let's say `api-audience`
    - **Mapper type** as `Audience`
    - **Included Client Audience** to the first **client_id** you added (`gatekeeper`).
3. Go back to the **Clients** menu on the sidebar then to do the following for both clients you created at the **Client Scopes** tab in their settings page:
    - Add the **Client scope** you created (`api`) to the assigned default client scopes.

Finally, you'll need to add an user into the same realm as the clients are be used by your API's client applications.

## Configuring gatekeeper to proxy requests to your application

**Keycloak-gatekeeper** will run as a sidecar inside your application **Pod**.
By default all routes are protected. For more details check its
[configuration options](https://www.keycloak.org/docs/latest/securing_apps/#configuration-options)

This example here doesn't use helm for the sake of simplicity, but it should be easy to adapt to your specific scenario.

### ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: gatekeeper-config
  namespace: mynamespace
data:
  keycloak-gatekeeper.conf: |+
    # is the url for retrieve the OpenID configuration - normally the <server>/auth/realms/<realm_name>
    discovery-url: https://login.syseleven.de/auth/realms/REALM_NAME
    # skip tls verify
    skip-openid-provider-tls-verify: true
    # the client id for the 'client' application
    client-id:  gatekeeper   # the secret associated to the 'client' application
    client-secret: <CLIENT_SECRET>
    # the interface definition you wish the proxy to listen, all interfaces is specified as ':<port>', unix sockets as unix://<REL_PATH>|</ABS PATH>
    listen: :3000
    # whether to enable refresh tokens
    enable-refresh-tokens: true
    secure-cookie: false
    enable-logging: true
    enable-default-deny: true
    enable-session-cookies: true
    no-redirects: true
    ingress.enabled: true
    debug: false
    # the encryption key used to encode the session state
    encryption-key: <random char sequence>
    # the upstream endpoint which we should proxy request
    upstream-url: http://127.0.0.1:8080/
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myservice
  namespace: mynamespace
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myservice
  template:
    metadata:
      labels:
        app: myservice
    spec:
      containers:
      - name: myservice
        image: myservice:latest
        ports:
        - containerPort: 8080
      - name: gatekeeper
        image: keycloak/keycloak-gatekeeper:7.0.0
        args:
        - --config=/etc/keycloak-gatekeeper.conf
        ports:
        - containerPort: 3000
          name: service
        volumeMounts:
        - name: gatekeeper-config
          mountPath: /etc/keycloak-gatekeeper.conf
          subPath: keycloak-gatekeeper.conf
      volumes:
      - name : gatekeeper-config
        configMap:
          name: gatekeeper-config
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: myservice
  name: myservice
  namespace: mynamespace
spec:
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: service
  selector:
    app: myservice
  type: ClusterIP
```

## How to access the protected URIs

In order to authenticate against an application proxied by **keycloak-gatekeeper**,
only two steps are needed:

1. Retrieve the token
2. Use it as `Authorization` header on requests

### Authenticating - Retrieving your token

To acquire your token you need to call **Keycloak API**,
which can be done as the example below:

```bash
curl $CURL_OPTS -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=$YOUR_USERNAME" \
  -d "password=$PASSWORD"   \
  -d 'grant_type=password'  \
  -d "client_id=$CLIENT_ID"   \
  -d "client_secret=$CLIENT_SECRET"  \
  "https://login.syseleven.de/auth/realms/YOUR_REALM/protocol/openid-connect/token" | jq .
```

The response will be in the following format:

```json
{
  "access_token": "eyJhbGci...",
  "expires_in": 28800,
  "refresh_expires_in": 1800,
  "refresh_token": "eyUqi...",
  "token_type": "bearer",
  "not-before-policy": 0,
  "session_state": "5ad82043-7c15-4497-bf80-2428a270eebb",
  "scope": "api profile email"
}
```

Notice the scope `api` is included. Get the `"access_token"`, it will be used
as an Authentication header to access your application.

### Accessing the protected URL

Now that you have the token, you need to included it in the `Authorization` header,
as the example shows:

```bash
curl https://yourapp.net/api \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer eyJhbGc...'
```

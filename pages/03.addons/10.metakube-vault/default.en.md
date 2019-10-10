---
title: 'Vault'
published: true
taxonomy:
    tag:
        - metakube
        - addon
        - hashicorp
        - vault
        - secrets
        - passwords
        - certificates
        - credentials
---

With [Vault](https://www.vaultproject.io/) from HashiCorp you can manage and store credentials and other sensitive data in a secure way.

You can find more information on

* [Vault documentation](https://www.vaultproject.io/docs/)
* [Vault learning platform](https://learn.hashicorp.com/vault)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible:

## Getting root token and unseal keys

After the installation of the add-on you can get the Vault root token and unseal key with our secret sharing service `secrets.syseleven.de`. You can get a one-time link to fetch them with:

```bash
kubectl get secret -n syseleven-vault initial-keys -o "jsonpath={.data.link}" | base64 --decode
```

These tokens should be kept in a secure location.

## Accessing the Vault UI

You can reach the Vault Web UI from the Cluster Detail Page. In addition to the standard SysEleven authentication, you have to log into Vault itself as well, either with a root Token or with any other authentication method that you can set up.

For more information see [Auth Methods](https://www.vaultproject.io/docs/auth/index.html).

## Using the Vault CLI

By default the Vault API is not exposed publicly but only available within your cluster. If you want to use the Vault CLI to manage and operate your cluster, the easiest option is, to start a pod with the Vault CLI in your cluster:

```console
$ kubectl run --generator=run-pod/v1 vault-cli --image=vault --env VAULT_ADDR=http://syseleven-vault.syseleven-vault:8200 -i --tty --rm --command sh
If you don't see a command prompt, try pressing enter.
/ # vault status
/ # exit
Session ended, resume using 'kubectl attach vault-cli -c vault-cli -i -t' command when the pod is running
pod "vault-cli" deleted
```

## Unsealing Vault

Initially, or if a Vault Pod is updated or restarted, Vault seals itself to prevent malicious access. In order to use Vault, you have to unseal it with your unseal keys. The easiest way for this is by using the unseal dialog from the cluster detail page.

See also [Seal/Unseal](https://www.vaultproject.io/docs/concepts/seal.html).

## Accessing Vault from within Kubernetes

When installing Vault it is automatically set up so that services running inside of Kubernetes can access it. The following short tutorial shows you how this can work:

First create a namespace for the tutorial

```consol
kubectl create namespace vault-tutorial
```

Then write a secret into vault:

```console
$ kubectl run --generator=run-pod/v1 vault-cli --image=vault --env VAULT_ADDR=http://syseleven-vault.syseleven-vault:8200 -i --tty --rm --command sh
If you don't see a command prompt, try pressing enter.
/ # vault login <your-generated-root-token>
...
/ # vault kv put secret/hello foo=world
Success! Data written to: secret/hello
```

Then create a service account in Kubernetes for the Pod that should access Vault:

```console
kubectl create serviceaccount my-service --namespace vault-tutorial
```

Next you have to allow this new service account to access the created secret:

```console
$ kubectl run --generator=run-pod/v1 vault-cli --image=vault --env VAULT_ADDR=http://syseleven-vault.syseleven-vault:8200 -i --tty --rm --command sh
If you don't see a command prompt, try pressing enter.
/ # vault login <your-generated-root-token>
...
/ # echo 'path "secret/hello" { capabilities = ["read", "list"] }' | vault write sys/policy/my-service-policy policy=-
Success! Data written to: sys/policy/my-service-policy
/ # vault write auth/kubernetes/role/my-service \
     bound_service_account_names=my-service \
     bound_service_account_namespaces=vault-tutorial \
     policies=my-service-policy \
     ttl=1h
Success! Data written to: auth/kubernetes/role/my-service
```

You can now use the token of this ServiceAccount to access Vault and retrieve the secret. There are multiple options for doing it, one is to have an init container that reads a secret from Vault and writes the data onto a shared volume:

```bash
cat <<'EOF' | kubectl apply --namespace vault-tutorial -f -
kind: Deployment
apiVersion: apps/v1
metadata:
  name: vault-tutorial-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vault-tutorial-app
  template:
    metadata:
      labels:
        app: vault-tutorial-app
    spec:
      serviceAccountName: my-service
      volumes:
      - name: secrets
        emptyDir: {}
      containers:
      - name: nginx-hello
        image: nginxdemos/hello:0.2
        volumeMounts:
          - name: secrets
            mountPath: /usr/share/nginx/html
        livenessProbe:
          httpGet:
            path: /
            port: 80
          timeoutSeconds: 1
        readinessProbe:
          httpGet:
            path: /
            port: 80
          timeoutSeconds: 1
      initContainers:
      - name: vault
        image: vault:latest
        env:
          - name: VAULT_ADDR
            value: http://syseleven-vault.syseleven-vault:8200
        command:
          - sh
          - -c
          - VAULT_TOKEN=$(vault write -field=token auth/kubernetes/login role=my-service jwt=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)) vault read -field=foo secret/hello >  /etc/secrets/index.html
        volumeMounts:
          - name: secrets
            mountPath: /etc/secrets
---
kind: Service
apiVersion: v1
metadata:
  name: vault-tutorial-app
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: vault-tutorial-app
EOF
kubectl --namespace vault-tutorial port-forward service/vault-tutorial-app 8080:80
```

If you access `http://localhost:8080` now this nginx will respond with `world.

For some other options see for example:

* [bank-vaults](https://banzaicloud.github.io/bank-vaults/)
* [kubernetes-mutation-webhook-vault-secrets](https://github.com/innovia/kubernetes-mutation-webhook-vault-secrets)
* [vault-sidekick](https://github.com/UKHomeOffice/vault-sidekick)
* [vault-creds](https://github.com/uswitch/vault-creds)

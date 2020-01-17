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

You can find more information about Vault here:

* [Vault documentation](https://www.vaultproject.io/docs/)
* [Vault learning platform](https://learn.hashicorp.com/vault)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible for Vault:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 128Mi |
| CPU Limits | What is the maximum of used milli CPUs | 200m |
| Memory Requests | What is the maximum of used memory | 512Mi |

The following customization options are possible for Consul:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 128Mi |
| CPU Limits | What is the maximum of used milli CPUs | 200m |
| Memory Requests | What is the maximum of used memory | 512Mi |
| Storage size | How large should the persistent data volume be | 10Gi |

## Getting root token and unseal keys

After the installation of the Add-On you can retrive the Vault root token and unseal keys with our secret sharing service `secrets.syseleven.de`. You recieve a one-time link to fetch them with:

```bash
kubectl get secret -n syseleven-vault initial-keys -o "jsonpath={.data.link}" | base64 --decode
```

These tokens should be kept in a secure location. We cannot recreate them for you. If they are lost you will not be able to access your data.

## Accessing the Vault UI

You can reach the Vault Web UI from the cluster detail page. In addition to the standard SysEleven authentication, you have to log into Vault itself as well, either with a root token or with any other authentication method that you can setup.

For more information see [Auth Methods](https://www.vaultproject.io/docs/auth/index.html).

## Using the Vault CLI

By default the Vault API is not exposed publicly and only available within your cluster. If you want to use the Vault CLI to manage and operate your cluster, the easiest option is, to start a pod with the Vault CLI in your cluster:

```console
$ kubectl run --generator=run-pod/v1 vault-cli --image=vault --env VAULT_ADDR=http://syseleven-vault.syseleven-vault:8200 -i --tty --rm --command sh
If you don't see a command prompt, try pressing enter.
/ # vault status
/ # exit
Session ended, resume using 'kubectl attach vault-cli -c vault-cli -i -t' command when the pod is running
pod "vault-cli" deleted
```

## Unsealing Vault

Initially Vault is unsealed for you during the installation process. If a Vault Pod is updated or restarted, Vault seals itself to prevent malicious access. In order to use Vault, you have to unseal it with your unseal keys. The easiest way to do this is by using the unseal dialog from the cluster detail page.

See also [Seal/Unseal](https://www.vaultproject.io/docs/concepts/seal.html).

## Accessing Vault from within Kubernetes

When installing Vault it is automatically set up so that services running inside of Kubernetes can access it. The following short tutorial shows you how this can work:

First create a namespace for the tutorial

```console
kubectl create namespace vault-tutorial
```

Then write a secret to vault:

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

Next you grant access for the service account to the created secret with a policy:

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

You can now use the token of this service account to access Vault and retrieve the secret. There are multiple options for doing this. One option is to have an init container that reads a secret from Vault and writes the data to a shared volume:

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

If you access `http://localhost:8080` now the nginx will respond with world.

For some other options see for example:

* [bank-vaults](https://banzaicloud.github.io/bank-vaults/)
* [kubernetes-mutation-webhook-vault-secrets](https://github.com/innovia/kubernetes-mutation-webhook-vault-secrets)
* [vault-sidekick](https://github.com/UKHomeOffice/vault-sidekick)
* [vault-creds](https://github.com/uswitch/vault-creds)


## Backup and Restore

The Managed Vault Add-on comes with automatic backups of your Vault cluster every hour. You may restore your vault
cluster to the last stored backup by running the following job:

```bash
cat <<'EOF' | kubectl apply --namespace syseleven-vault -f -
apiVersion: batch/v1
kind: Job
metadata:
  name: restore-vault
  namespace: syseleven-vault
spec:
  template:
    metadata:
      name: restore-vault
    spec:
      restartPolicy: Never
      serviceAccountName: syseleven-vault
      containers:
        - name: vault-restore
          image: syseleven/metakube-s3-uploader:0.2.0
          envFrom:
            - secretRef:
                name: cloud-config
          env:
            - name: CONSUL_HTTP_ADDR
              value: syseleven-consul-consul-server:8500
          command:
            - /bin/bash
            - -c
            - |
              create-credentials && restore-consul
          imagePullPolicy: Always
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
EOF
```

After the job has completed your Vault cluster might go into sealed state. To unseal Vault refer the the relevant section above.

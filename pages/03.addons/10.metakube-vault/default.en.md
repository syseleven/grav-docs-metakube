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

After the installation of the addon you can get the Vault root token and unseal key with our secret sharing service secrets.syseleven.de. You can get a one-time link to fetch them with:

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
$ kubectl run --generator=run-pod/v1 vault-cli --image=vault -i --tty --rm --command sh
If you don't see a command prompt, try pressing enter.
/ # export VAULT_ADDR=http://syseleven-vault.syseleven-vault:8200
/ # vault status
/ # exit
Session ended, resume using 'kubectl attach vault-cli -c vault-cli -i -t' command when the pod is running
pod "vault-cli" deleted
```

## Unsealing Vault

Initially, or if a Vault Pod is updated or restarted, Vault seals itself to prevent malicious access. In order to use Vault, you have to unseal it with your unseal keys. The easiest way for this is by using the unseal dialog from the cluster detail page.

See also [Seal/Unseal](https://www.vaultproject.io/docs/concepts/seal.html).

## Accessing Vault from within Kubernetes

---
title: 'Role Based Access Control with ServiceAccounts'
published: true
taxonomy:
    tag:
        - kubernetes
        - ServiceAccounts
        - rbac
        - user management
---

By default a MetaKube cluster only comes with one admin token pre-configured to use. This admin token is included in the kubeconfig that you can [download](../06.download-the-kubeconfig/default.en.md) in the dashboard.

This tutorial shows, how you can create ServiceAccounts with more limited access that you can use for a personalized access to the cluster. This is especially useful for technical accounts like your CI system.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Creating a ServiceAccount

Having admin rights, first create a personalized ServiceAccount for a user that should have limited access

```shell
$ kubectl create serviceaccount john-doe
serviceaccount/john-doe created
```

After that you can retrieve an API token by first fetching the name of the secret where the ServiceAccount token is stored

```shell
$ kubectl get serviceaccounts john-doe -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: "2019-02-25T14:59:26Z"
  name: john-doe
  namespace: default
  resourceVersion: "3342411"
  selfLink: /api/v1/namespaces/default/serviceaccounts/metakube
  uid: f20cfa03-390d-11e9-9906-0a580af40ae2
secrets:
- name: john-doe-token-n9w5x
```

Then retrieving this secret:

```shell
$ kubectl get secrets john-doe-token-n9w5x -o yaml
apiVersion: v1
data:
  ca.crt: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  namespace: XXXXX==
  token: bWFuYWdlZDprdWJlcm5ldGVzPW1ldGFrdWJl
kind: Secret
metadata:
  annotations:
    kubernetes.io/service-account.name: john-doe
    kubernetes.io/service-account.uid: f20cfa03-390d-11e9-9906-0a580af40ae2
  creationTimestamp: "2019-02-25T14:59:27Z"
  name: john-doe-token-n9w5x
  namespace: default
  resourceVersion: "3342410"
  selfLink: /api/v1/namespaces/default/secrets/john-doe-token-n9w5x
  uid: f21405c0-390d-11e9-9024-0a580af4096f
type: kubernetes.io/service-account-token
```

And base64 decoding the token inside it:

```shell
$ echo "bWFuYWdlZDprdWJlcm5ldGVzPW1ldGFrdWJl" | base64 --decode
managed:kubernetes=john-doe
```

After that you can create a new kubeconfig based on the admin one you download by changing the user name and token in it. See also [Download the kubeconfig](../06.download-the-kubeconfig/default.en.md). You can now use this kubeconfig to access the cluster with the user "john-doe".

### Adding permissions for a user

By default a ServiceAccount has no permissions inside of the cluster if it is not bound to any Roles or ClusterRoles. In this example the account is bound to the pre-defined ClusterRole `view`:

```shell
$ kubectl create clusterrolebinding john-doe --clusterrole=view --serviceaccount=default:john-doe
clusterrolebinding.rbac.authorization.k8s.io/john-doe created
```

You can check the permissions of a role with:

```shell
$ kubectl get clusterrole view -o yaml
...
```

For more information see [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

### Test the ServiceAccount

When you use the newly created kubeconfig with the new ServiceAccount user and token, you won't be allowed to access any nodes:

```shell
$ kubectl get nodes
Error from server (Forbidden): nodes is forbidden: User "system:serviceaccount:default:john-doe" cannot list resource "nodes" in API group "" at the cluster scope
```

You are allowed to access pods:

```shell
$ kubectl get pods
NAME                                             READY   STATUS    RESTARTS   AGE
ghost-5b496bfd64-v24ng                           1/1     Running   0          161m
ghost-mariadb-0                                  1/1     Running   0          10d
webapp-5f45f4fb96-jdxvp                          1/1     Running   0          10d
wordpress-mariadb-0                              1/1     Running   0          10d
wordpress-wordpress-5b8c898c86-xkwzr             1/1     Running   0          161m
```

But you are not allowed to delete a pod:

```shell
$ kubectl delete pods ghost-5b496bfd64-v24ng
Error from server (Forbidden): pods "ghost-5b496bfd64-v24ng" is forbidden: User "system:serviceaccount:default:john-doe" cannot delete resource "pods" in API group "" in the namespace "default
```

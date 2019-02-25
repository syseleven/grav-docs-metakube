---
title: 'Using service accounts with rbac roles'
published: true
taxonomy:
    tag:
        - kubernetes
        - service accounts
        - rbac
        - user management
---

The default MetaKube Cluster comes with one admin account to use.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Creating your first view only service account

```shell
$ kubectl create serviceaccount metakube
serviceaccount/metakube created
```

## Fetch the service account with the service token secret

```shell
$ kubectl get serviceaccounts metakube -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: "2019-02-25T14:59:26Z"
  name: metakube
  namespace: default
  resourceVersion: "3342411"
  selfLink: /api/v1/namespaces/default/serviceaccounts/metakube
  uid: f20cfa03-390d-11e9-9906-0a580af40ae2
secrets:
- name: metakube-token-n9w5x
```

### Fetch the users service token

```shell
$ kubectl get secrets metakube-token-n9w5x -o yaml
apiVersion: v1
data:
  ca.crt: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  namespace: XXXXX==
  token: bWFuYWdlZDprdWJlcm5ldGVzPW1ldGFrdWJl
kind: Secret
metadata:
  annotations:
    kubernetes.io/service-account.name: metakube
    kubernetes.io/service-account.uid: f20cfa03-390d-11e9-9906-0a580af40ae2
  creationTimestamp: "2019-02-25T14:59:27Z"
  name: metakube-token-n9w5x
  namespace: default
  resourceVersion: "3342410"
  selfLink: /api/v1/namespaces/default/secrets/metakube-token-n9w5x
  uid: f21405c0-390d-11e9-9024-0a580af4096f
type: kubernetes.io/service-account-token
```

### Copy the base64 encoded token from the secret and decode it

```shell
$ echo "bWFuYWdlZDprdWJlcm5ldGVzPW1ldGFrdWJl" | base64 --decode
managed:kubernetes=metakube
```

### Copy the decoded token to a kube config file

See [Download the kubeconfig](../06.download-the-kubeconfig/default.en.md)

### Add a cluster role binding (cluster wide policy) to the users service account

```shell
$ kubectl create clusterrolebinding serviceaccounts-view --clusterrole=view --serviceaccount=default:metakube
clusterrolebinding.rbac.authorization.k8s.io/serviceaccounts-view created
```

### Test the service account with the view cluster role

```shell
$ kubectl get nodes
Error from server (Forbidden): nodes is forbidden: User "system:serviceaccount:default:metakube" cannot list resource "nodes" in API group "" at the cluster scope
```

I am not allowed to list nodes lets try and get pods instead

```shell
$ kubectl get pods
NAME                                             READY   STATUS    RESTARTS   AGE
ghost-5b496bfd64-v24ng                           1/1     Running   0          161m
ghost-mariadb-0                                  1/1     Running   0          10d
webapp-5f45f4fb96-jdxvp                          1/1     Running   0          10d
wordpress-mariadb-0                              1/1     Running   0          10d
wordpress-wordpress-5b8c898c86-xkwzr             1/1     Running   0          161m
```

I am allowed to view pods but not nodes. What about deleting pods. I would expect this not to work with a view account. Lets try and delete one of the pods

```shell
$ kubectl delete pods ghost-5b496bfd64-v24ng
Error from server (Forbidden): pods "ghost-5b496bfd64-v24ng" is forbidden: User "system:serviceaccount:default:metakube" cannot delete resource "pods" in API group "" in the namespace "default
```

That looks good the created service account with view privileges cannot delete pods.

### You may view the contents of the "view cluster" role with this commmand

```shell
$ kubectl get clusterrole view -o yaml
no output shown here
```

You can use this clusterrole as a base to create addional clusteroles. Please be careful when using the cluster-admin clusterrole. This clusterrole is equivalent to having root access to a linux server.

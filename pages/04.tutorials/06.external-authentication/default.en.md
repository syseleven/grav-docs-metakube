---
title: 'Authenticating with SysEleven Login and external authentication system'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - gui
        - login
        - authentication
        - ldap
        - openid-connect
        - saml
---

This tutorial describes how you can activate authentication on your cluster with SysEleven Login (powered by [KeyCloak](https://www.keycloak.org)) and how to connect it to an external user management system.

## Configure SysEleven Login realm

SysEleven Login allows you to manager users and groups that should have access to SysEleven services as well as your Kubernetes clusters yourself. You can also set up user federation to external identity providers like LDAP, Active Directory or any OpenID Connect provider. For that you are provided with your own realm that only contains your users and settings.

To configure your realm go to the SysEleven Realm Admin Login https://login.syseleven.de/auth/admin/{REALM-NAME}/console and log in with the provided admin credentials.

You can find a detailed documentation on managing users, groups, user federation and identity providers in the official [KeyCloak documentation](https://www.keycloak.org/docs/latest/server_admin/).

## SysEleven Login Realm URL's

SysEleven Admin User Login: https://login.syseleven.de/auth/admin/{REALM-NAME}/console

SysEleven User Realm Login: https://login.syseleven.de/auth/realm/{REALM-NAME}/account

## Configure cluster authentication

By default it is only possible to authenticate at a cluster with the [central admin token](../06.download-the-kubeconfig/default.en.md). In order to use SysEleven Login via OpenID Connect, you have to go the cluster detail page and activate it in the Auth tab.

In the configuration dialog choose "SysEleven Login" and provide the realm that you want to use for authentication. This is already pre-filled with the realm that you used to log into the MetaKube dashboard, but also can be changed to another realm.

## Download OpenID KubeConfig

Once you configured cluster authentication with OpenID Connect, you can download a specialized KubeConfig with your OpenID settings from the cluster detail page. If you haven't used this KubeConfig fo a login (see below), you can safely share this with your colleagues since it does not contain any sensitive, personalized information yet and without logging in, you can not authenticate at the cluster.

## Log into your cluster

Once you downloaded the OpenID KubeConfig, you can use it to log into your cluster. The easiest way to do this is with [kubelogin](https://github.com/int128/kubelogin).

Follow the installation instructions and then run either

```shell
kubelogin
```

or

```shell
kubectl odic-login
```

This will open a browser window where you can login and then write an OpenID access and refresh token into your KubeConfig. The access token will be refreshed automatically by kubectl as long as the refresh token is still valid.

## Authorize a user or group to use your cluster

When you authenticate at a cluster with OpenID Connect, you don't have any permissions for any operations in the cluster by default.

In order to give a user or a group of users permissions in the cluster, you have to authenticate with the [central admin token](../06.download-the-kubeconfig/default.en.md) and then add either a ClusterRoleBinding or a namespace bound RoleBinding that maps the user or group to a ClusterRole or Role which specifies the permission the user or group should have.

### Via the MetaKube dashboard

The easiest way to give permissions to a user or group is to use the builtin RBAC support in the MetaKube dashboard. In the authorization tab of the cluster detail page, you can grant view, edit or admin permissions to individual users. You can grant these permissions for the whole cluster or limit it to a single namespace. In the background these are translated to ClusterRoleBindings and RoleBindings.
For the e-mail address or group name you have to use the same information which is stored in SysEleven Login.

### Manually

If the default roles are not enough for your use case and you need more fine-grained control, you can manage the ClusterRoleBindings and RoleBindings manually. Remember, you need to use the same e-mail addresses or group name as in SysEleven Login here as well. Using the simple web interface and manually managed roles at the same time is also possible.

The following ClusterRoleBinding example would map the cluster-admin ClusterRole to the user "user@xample.com" and all users in the SysEleven Login Group "kubernetes". The cluster-admin ClusterRole allows to perform all operations on all resources in the whole cluster.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: oidc-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: kubernetes
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: user@xample.com
```

Kubernetes already provides some predefined ClusterRoles:

* view
* edit
* cluster-admin

You can of course also create your own ClusterRoles or Roles. For more information see [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). If you manage a lot of roles manually, [rbac-manager](https://github.com/FairwindsOps/rbac-manager) can make it easier.

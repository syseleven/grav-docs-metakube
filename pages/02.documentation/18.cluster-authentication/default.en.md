---
title: 'Cluster Authentication'
taxonomy:
    tag:
        - metakube
        - authentication
        - login
        - openid-connect
        - ldap
        - saml
---

There are 3 ways to authenticate against a MetaKube cluster:

## Authentication with central admin token

For every cluster we automatically generate a central admin token that gives you full admin access to the cluster. This token is the same for every logged in user.

For more information see [Download a kubeconfig with a central admin token](../../04.tutorials/06.download-the-kubeconfig/default.en.md).

## Authentication with ServiceAccount tokens

You can authenticate against a cluster with a ServiceAccount token. This is especially useful for technical accounts like your CI system.

For more information see [Role Based Access Control with ServiceAccounts](../../04.tutorials/24.using-service-accounts-with-rbac/default.en.md).

## Authentication with OpenID Connect over SysEleven Login (beta)

Optionally you can authenticate against a cluster with our SysEleven Login OpenID Provider which is powered by [Keycloak](https://www.keycloak.org/). There you are provided with your own realm in which you can either manage your users and groups manually, or that you can connect to an external User Management System like LDAP, Active Directory or any SAML or OpenID Connect Provider.

For more information see [Authenticating with SysEleven Login and external authentication system](../../04.tutorials/06.external-authentication/default.en.md).

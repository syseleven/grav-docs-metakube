---
title: 'Projects'
taxonomy:
    tag:
        - metakube
        - gui
---

With projects you can group your MetaKube clusters and SSH keys and allow other
MetaKube users to also use or maintain your cluster, by inviting them as a
member to the project.

## Switching the current project

You can see and switch the currently active project with the dropdown menu in
the upper left corner.

![Show current project](show-project.png)

![Switch project](switch-project.png)

## Adding a new project

In the list of all your projects you can create new projects or delete existing
ones. Additionally you can find an `Add Project` button in the dropdown menu in
the upper left corner.

## Adding members to a project

In the members list of a project you can add or remove additional members to
your project.  All members can [download a kubeconfig](../../03.Tutorials/06.download-the-kubeconfig/default.en.md)
with full admin rights to interact with the Kubernetes cluster itself.  The
following permission roles are available:

### Owner

The creator and owner of a project has full admin access to all clusters and can
add and remove members.

### Editor

An editor has full admin access to all clusters, but can not add or remove
members.

### Viewer

A viewer can see clusters, but neither add new ones nor edit or delete existing
clusters.

---
title: SysEleven Container Registry
page-toc:
    active: false
body_classes: 'title-center title-h1h2'
---

[SysEleven Container Registry (SCR)](https://scr.syseleven.de) is the managed registry for container images and Helm charts from [SysEleven](https://www.syseleven.de).

[SCR](https://scr.syseleven.de) is based on the [Cloud Native Comuputing Foundation](https://cncf.io) incubated, open source project [Harbor](https://goharbor.io/)


## Architectural Overview

The following document explains in detail the architechture of [Harbor](https://goharbor.io/)

[Harbor Architecture Overview](https://github.com/goharbor/harbor/wiki/Architecture-Overview-of-Harbor)

## User Guide

SysEleven Container Registry follows the same user workflow as Harbor, following upstream tutorials can help SCR users
perform common tasks such as creating and managing new projects, adding repositories to projects, managing image label etc.

- [Complete User Guid](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#user-guide)

The following sections point to frequently used user workflows from the above user guide.

### Working with projects

This section describes how users with the developer, master, and project administrator roles manage and participate in SCR projects.

- [Managing projects](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#managing-projects)

### Working with images and tags

This section describes how to work with images and tags in SysEleven Container Registry.

- [Pushing and pulling images using docker CLI](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#pulling-and-pushing-images-using-docker-client)
- [Managing image labels](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#managing-labels)
- [Re-Tagging images](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#retag-images)
- [Working with tag retention rules](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#tag-retention-rules)

### Vulnerability Scanning

SCR provides static analysis of vulnerabilities in images through the open source [Clair](https://github.com/coreos/clair) project.

You can manually initiate scanning on a particular image, or on all images in SCR. Additionally, you can also set a policy to automatically scan all of the images at specific intervals.

- [Vulnerability Scanning with Clair](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#vulnerability-scanning-via-clair)
- [Configuring CVE whitelists](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#configure-cve-whitelists)

### Working with Helm charts

Helm is a package manager for kubernetes applications. SysEleven Container Registry comes with built in support for helm repositories.
The following tutorial will guide you on how to store and manager your helm charts with SCR.

- [Managing helm charts via portal](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#manage-helm-charts-via-portal)
- [Working with Helm CLI](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#working-with-helm-cli)

### Working with Robot Accounts

SCR allows users to create robot accounts in each project, to use for automated operation. To create and use robot accounts follow this [tutorial](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#robot-account).

### Working with Webhooks

SCR project administrator can configure webhook endpoint inside a project, which can be triggered based on certain events. This [tutorial](https://github.com/goharbor/harbor/blob/v1.10.0/docs/user_guide.md#webhook-notifications) will guide you on how
 to use webhooks with SCR.

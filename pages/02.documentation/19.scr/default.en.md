---
title: SysEleven Container Registry
page-toc:
    active: false
body_classes: 'title-center title-h1h2'
---

[SysEleven Container Registry (SCR)](https://scr.syseleven.de) is the managed registry for container images and Helm charts from [SysEleven](https://www.syseleven.de).

[SCR](https://scr.syseleven.de) is based on the [Cloud Native Computing Foundation](https://cncf.io) incubated, open source project [Harbor](https://goharbor.io/)


## Architectural Overview

The following document explains the architechture of [Harbor](https://goharbor.io/) in detail.

[Harbor Architecture Overview](https://github.com/goharbor/harbor/wiki/Architecture-Overview-of-Harbor)

## User Guide

SysEleven Container Registry follows the same user workflow as Harbor, following upstream tutorials can help SCR users
perform common tasks such as creating and managing new projects, adding repositories to projects, managing images labels etc.

The following sections point to frequently used user workflows from the above user guide.

### Working with projects

This section describes how users with the developer, master, and project administrator roles manage and participate in SCR projects.

- [Creating projects](https://goharbor.io/docs/2.0.0/working-with-projects/create-projects/)
- [Configuring projects](https://goharbor.io/docs/2.0.0/working-with-projects/project-configuration/)

### Working with images and tags

This section describes how to work with images and tags in the SysEleven Container Registry.

- [Pushing and pulling images using docker CLI](https://goharbor.io/docs/2.0.0/working-with-projects/working-with-images/pulling-pushing-images/)
- [Managing image labels](https://goharbor.io/docs/2.0.0/working-with-projects/working-with-images/create-labels/)
- [Re-Tagging images](https://goharbor.io/docs/2.0.0/working-with-projects/working-with-images/retagging-images/)
- [Working with tag retention rules](https://goharbor.io/docs/2.0.0/working-with-projects/working-with-images/create-tag-retention-rules/)

### Vulnerability Scanning

SCR provides static analysis of vulnerabilities in images through the open source project [Trivy](https://github.com/aquasecurity/trivy).

You can manually initiate scanning on a particular image, or you can configure a project to scan all images when pushing to the registry.

- [Vulnerability Scanning with Trivy](https://goharbor.io/docs/2.0.0/administration/vulnerability-scanning/)
- [Configuring CVE whitelists](https://goharbor.io/docs/2.0.0/working-with-projects/project-configuration/configure-project-whitelist/)

### Working with Helm charts

Helm is a package manager for kubernetes applications. SysEleven Container Registry comes with built in support for helm repositories.
The following tutorial will guide you on how to store and manage your helm charts with SCR.

- [Managing Helm charts](https://goharbor.io/docs/2.0.0/working-with-projects/working-with-images/managing-helm-charts/)

### Working with Robot Accounts

SCR allows users to create robot accounts in each project, to use for automated operation. To create and use robot accounts follow this [tutorial](https://goharbor.io/docs/2.0.0/working-with-projects/project-configuration/create-robot-accounts/).

### Working with Webhooks

SCR project administrators can configure webhook endpoints inside a project, which can be triggered based on certain events. This [tutorial](https://goharbor.io/docs/2.0.0/working-with-projects/project-configuration/configure-webhooks/) will guide you on how
 to use webhooks with SCR.

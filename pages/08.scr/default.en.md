---
title: SysEleven Container Registry
page-toc:
    active: false
body_classes: 'title-center title-h1h2'
---

[SysEleven Container Registry (SCR)](https://scr.syseleven.de) is the managed registry for containers and Helm charts from [SysEleven](https://www.syseleven.de) - a Berlin based managed hosting company.

[SCR](https://scr.syseleven.de) is based on [Cloud Native Comuputing Foundation](cncf.io) incubated, open source project [Harbor](https://goharbor.io/)


## Architectural Overview

The following document explains in detail the architechture of [Harbor](https://goharbor.io/)

![Harbor Architecture Overview](https://github.com/goharbor/harbor/wiki/Architecture-Overview-of-Harbor)

## User Guide

SysEleven Container Registry follows the same user workflow as Harbor, following upstream tutorials can help SCR users
perform common tasks such as creating and managing new projects, adding repositories to projects, managing image label etc.

### Working with projects
This section describes how users with the developer, master, and project administrator roles manage and participate in SCR projects.
- [Creating new Projects](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/create_projects.md)
- [Configuring project properties](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/project_configuration.md)

### Working with images and tags
This section describes how to work with images and tags in SysEleven Container Registry.
- [Pushing and pulling images](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/pulling_pushing_images.md)
- [Managing image labels](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/create_labels.md)
- [Tagging images](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/retagging_images.md)
- [Working with tag retention rules](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/create_tag_retention_rules.md)
- [Working with tag immutability rules](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/create_tag_immutability_rules.md)

### Working with Helm charts
Helm is a package manager for kubernetes applications. SysEleven Container Registry comes with built in support for helm repositories.
The following tutorial will guide you on how to store and manager your helm charts with SCR.

- [Managing helm charts](https://github.com/goharbor/harbor/blob/master/docs/1.10/working_with_projects/managing_helm_charts.md)
 

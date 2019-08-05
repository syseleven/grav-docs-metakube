---
title: 'Velero Backups'
published: false
date: '22-07-2019 19:00'
taxonomy:
    tag:
        - metakube
        - backup
        - velero
---

The Velero Backups addon installs [Velero](https://velero.io/), a fully functional Kubernetes backup solution that you can use to safely backup and restore Kubernetes cluster resources and persistent volumes.

.The Velero installation will be pre-configured to store the created backups in the object storage solution of the cloud provider your cluster is running on (Quobyte S3 on SysEleven Stack, Amazon S3 on AWS).

See the [Backup tutorial](../../04.tutorials/18.create-backup-and-restore/default.en.md) for more information on using this addon.

## Installation

For information on how to install and uninstall an addon see [Addons](../default.en.md).

## Customization

There are no customization options.

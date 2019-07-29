---
title: 'Update the SysEleven Stack password'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - gui
menu: 'Update the Stack password'
---

## Update the master components

If you change your login password for SysEleven Stack, you also have to change it in all clusters you created with these credentials. To do that go the the detail page of your cluster and click on `Edit Settings` in the burger menu:

![Edit Settings dialog opened](image_edit-settings_01.png)

!! The cloud-config on the nodes will not be updated automatically. You have to recreate your worker nodes or update the cloud-config on every worker node.

## Update the worker nodes

To update the stack password in the cloud-config on the worker nodes, you can either recreate all worker nodes or use a script we provide to update all cloud-configs in place. To use the script clone our [migrations repository](https://github.com/syseleven/metakube-migration), which includes the `update-node-passwords` script. You can execute the script on any unix-based client system (including macOS).
Make sure that your $KUBECONFIG is pointed to the cluster you want to upgrade, and you have ssh access to the nodes. The script will ask you for the changed password, ssh into all the nodes, update the cloud config file (`/etc/kubernetes/cloud-config`) and restart the kubelet.

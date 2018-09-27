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

If you change your login password for SysEleven Stack, you also have to change it in all clusters you created with these credentials.
To do that go the the detail page of your cluster and click on `Edit Settings` in the burger menu

![Edit Settings dialog opened](image_edit-settings_01.png)

!! The cloud-config on the nodes will not be updated. You have to recreate your worker nodes or update the cloud-config on every
worker node. For the latter, we're providing a script that automates this for you: Repository
<https://github.com/syseleven/metakube-migration>, script name `update-node-passwords.sh`. Just clone this repository and run
the `update-node-passwords.sh` script on any Unix-based (including macOS) client system. Make sure that your $KUBECONFIG is
pointed to the cluster you want to upgrade, and you have ssh access to the nodes. The script will ask you for the changed
password. It will then ssh into all the nodes, update the cloud config file (`/etc/kubernetes/cloud-config`) with the
new password, and restart kubelet.
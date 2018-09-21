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

!! The cloud-config on the nodes will not be udated. You have to recreate your nodes or update the cloud-config manually (edit the file `/etc/kubernetes/cloud-config`).
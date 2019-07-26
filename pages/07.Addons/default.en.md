---
published: true
title: MetaKube Addons
---

MetaKube addons are fully managed services that you can optionally install into your MetaKube cluster.

## Installation of addons

You can install and addon by clicking on the '+' button of the addon in the addon tab.

![Install addon](install-addon.png)

If the addon has any configuration options, you can configure them in the installation dialog.

![Configure addon](addon-config.png)

## Deinstallation of addons

To remove an addon from you cluster, click the delete icon of the addon and confirm the deletion. Note that all the resources of the addon including all persistent data will be removed.

![Deinstall addon](delete-addon.png)

## Access to web interfaces of addons

In the "Addon Web Interfaces" list you can access any web interfaces the installed addons provide. Note that the button is only clickable if the web interface is up and running.

![Web interfaces](web-interfaces.png)

## Addon update process

Since the addons are fully managed, version updates will be handled automatically by SysEleven after careful testing.

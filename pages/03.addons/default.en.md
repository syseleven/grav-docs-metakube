---
published: true
title: MetaKube Addons
---

MetaKube Addons are a fully managed services that you can optionally install into your MetaKube cluster.

## Installation of addons

You can install an addon either during cluster creation or later into an already running cluster.

During cluster creation, there is a wizard page that lets you choose the addons to install:

![Install addon during cluster creation](../04.tutorials/02.create-a-cluster/image_cluster-settings_04.png)

See the [cluster creation tutorial](../04.tutorials/02.create-a-cluster/default.en.md) for more details.

In an already running cluster, select the Addons tab in the cluster overview to get an overview of the available addons:

![Install addon into a runnin cluster](install-addon.png)

Both these user interfaces look and work the same. You install an addon by clicking on the '+' button of the addon in the addon tab.

If the addon has any configuration options, you can configure them in the installation dialog.

![Configure addon](addon-config.png)

### Installation channels

For several addons we are providing two installation channels, a "beta" channel and a "stable" channel. Updates and changes on these addons, will always be rolled out first on the beta channel, and one week after this on the stable channel. Within one cluster you can only install either the beta or the stable channel addon, but not both. This allows to test an addon with any custom integrations in a development or staging cluster by deploying the beta channel there before an update is rolled out to a production cluster where the stable channel is used. The installation channel can not be changed while an addon is installed. To change the channel you have to uninstall the addon and re-install it.

## Deinstallation of addons

To remove an addon from your cluster, click on the delete icon of the addon and confirm the deletion. Note that all the resources of the addon including all persistent data will be removed.

![Deinstall addon](delete-addon.png)

## Access to the web interfaces of addons

In the "Addon Web Interfaces" list you can access any web interfaces the installed addons provide. Note that the button is only clickable if the web interface is up and running.

![Web interfaces](web-interfaces.png)

## Addon update process

Since the addons are fully managed, version updates will be handled automatically by SysEleven after careful testing.

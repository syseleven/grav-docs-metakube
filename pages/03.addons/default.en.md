---
published: true
title: MetaKube Add-Ons
---

MetaKube Add-Ons are a fully managed services that you can optionally install into your MetaKube cluster.

## Installation of Add-Ons

You can install an Add-On either during cluster creation or later into an already running cluster.

During cluster creation, there is a wizard page that lets you choose the Add-Ons to install:

![Install Add-On during cluster creation](../04.tutorials/02.create-a-cluster/image_cluster-settings_04.png)

See the [cluster creation tutorial](../04.tutorials/02.create-a-cluster/default.en.md) for more details.

In an already running cluster, select the Add-Ons tab in the cluster overview to get an overview of the available Add-Ons:

![Install Add-On into a runnin cluster](install-addon.png)

Both these user interfaces look and work the same. You install an Add-On by clicking on the '+' button of the Add-On in the Add-Ons tab.

If the Add-On has any configuration options, you can configure them in the installation dialog.

![Configure Add-On](addon-config.png)

### Installation channels

For several Add-Ons we provide two installation channels, a "beta" channel and a "stable" channel. Updates and changes on these Add-Ons, will always be rolled out on the beta channel first, and one week later on the stable channel. Within one cluster you can only install either the beta or the stable channel Add-On, but not both. This allows to test an Add-On with any custom integrations in a development or staging cluster by deploying the beta channel there before an update is rolled out to a production cluster where the stable channel is used. The installation channel can not be changed while an Add-On is installed. To change the channel you have to uninstall the Add-On and re-install it.

## Deinstallation of Add-Ons

To remove an Add-On from your cluster, click on the delete icon of the Add-On and confirm the deletion. Note that all the resources of the Add-On including all persistent data will be removed.

![Deinstall Add-On](delete-addon.png)

## Access to the web interfaces of Add-Ons

In the "Add-On Web Interfaces" list you can access any web interfaces the installed Add-Ons provide. Note that the button is only clickable if the web interface is up and running.

![Web interfaces](web-interfaces.png)

## Add-On update process

Since the Add-Ons are fully managed, version updates will be handled automatically by SysEleven after careful testing.

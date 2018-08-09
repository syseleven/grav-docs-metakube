---
title: 'Add a worker node'
media_order: 'metakube_add-node_01.png,metakube_add-node_02.png,metakube_add-node_03.png,metakube_add-node_04.png'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - gui
        - node
---

[Add a new node](#add-a-new-node)
[Duplicate an existing node](#duplicate-an-existing-node)

## Add a new node

To add a new worker node, navigate to `Manage Cluster` and choose the cluster in which you want to add a new worker node. On the top right, click on `Add Node`

![Add Node button in the top right corner](image_add-node-button_01.png)

You will get a popup where you can select how many nodes you want to add and which image the nodes shall be based on. For an overview of the possible node flavors see the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/en/documentation/flavors).

![Dialog to add a new Node](image_add-node-dialog_01.png)

When you click on `Add Node(s)`, the popup will close and you can see the new nodes in pending status.

## Duplicate an existing node

To make the process of adding a node faster, you can also duplicate an existing node configuration.
Click on `duplicate node`.

![Duplicate Node button besides a node](image_duplicate-button_01.png)

And confirm the creation of the new node

![Dialog to duplicate Node](image_duplicate-dialog_01.png)

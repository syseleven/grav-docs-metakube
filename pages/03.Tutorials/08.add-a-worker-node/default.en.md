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

To add a new worker node, navigate to `Manage cluster` and choose the cluster, in which you want to add a new worker node. On the top right, click on `Add node`

![](metakube_add-node_01.png)

You will get a popup, where you can select how many nodes and which image the nodes shall be based on

![](metakube_add-node_02.png)

When you click on `Add Node(s)` the popup will close, and you can see the new nodes in pending status.

## Duplicating an existing node

To make the process of adding a node faster, you can also duplicate an existing node configuration.
Click on `duplicate node`.

![](metakube_add-node_03.png)

And confirm the creation of the new node

![](metakube_add-node_04.png)

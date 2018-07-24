---
title: 'How to use custom images for your worker nodes'
published: true
date: '23-07-2018 16:29'
taxonomy:
    tag:
        - metakube
        - cluster
        - node
menu: 'How to use custom images'
---

To use a custom image for your metakube clusters, you need to upload it SysEleven Stack and choose it in the workernode creation afterwards.

[Upload to SysEleven Stack](#upload-to-syseleven-stack)
[Choose in cluster creation](#choose-in-cluster-creation)

## Upload to SysEleven Stack

Log into the [SysEleven Stack dashboard](https://dashboard.cloud.syseleven.net/) and navigate to `Project -> Compute -> Images`. In the top right corner you can find the `Create Image` button to upload a new image

![Overview of the images tab](image_images-overview.png)

When you click on this button the image creation dialog will open. Enter a name, choose the image file and format and click on `Create Image`

![Image creation tab with filled out information](image_images-creation.png)

When the upload is finished the image will be visible in your images Tab.

## Choose in cluster creation

In the [cluster creation process](../02.create-a-cluster/default.en.md) you will be asked, which image and operating system (OS) you want to use. Choose the correct OS and enter the name you chose for your image before. The cluster will then be created with your custom image

![Cluster creation tab with custom image chosen](image_cluster-creation.png)

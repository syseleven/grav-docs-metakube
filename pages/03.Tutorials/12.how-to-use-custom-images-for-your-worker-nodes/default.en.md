---
title: 'How to use custom images for your worker nodes'
media_order: 'images_creation.png,images_overview.png,cluster_creation.png'
published: false
date: '23-07-2018 16:29'
taxonomy:
    tag:
        - metakube
        - cluster
        - node
menu: 'How to use custom images'
---

To use a custom image for your metakube clusters, you need to upload it SysEleven Stack and choose it in the workernode creation afterwards.

1 [Upload to SysEleven Stack](#upload-to-syseleven-stack)
2 [Choose in cluster creation](#choose-in-cluster-creation)

# Upload to SysEleven Stack

Log into the [SysEleven Stack dashboard](https://dashboard.cloud.syseleven.net/) and navigate to `Project -> Compute -> Images`. On the top right corner you can find the `Create Image` button to upload a new image

![Overview of the images tab](images_overview.png)

When you click on this button the image creation dialog will open. Enter a name and choose the image file and format

![Image creation tab with filled out information](images_creation.png)

When the upload is finished the image will be visible in your images Tab

# Choose in cluster creation

In the [cluster creation process](../create-a-cluster) you will be asked, which image and operating system (OS) you want to use. Choose the correct OS and enter the name you chose for your image before. The cluster will then be created with your custom image

![Cluster creation tab with custom image chosen](cluster_creation.png)
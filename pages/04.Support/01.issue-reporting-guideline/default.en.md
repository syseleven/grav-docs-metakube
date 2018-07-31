---
title: Issue reporting guideline
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - support
---

The following document describes the required form and level of details when reporting issues. It is not required but helps to investigate issues faster. It serves as a guideline on how to report issues.

* [Required information for faster resolutions](#required-information-for-faster-resolutions)

* [Steps you can take in the meantime](#steps-you-can-take-in-the-meantime)

## Required information for faster resolutions

To minimize the time to resolution we need as much of the following information as possible:

* [How can we contact you?](#how-can-we-contact-you?)

* [What is the desired behavior and what do you observe?](#what-is-the-desired-behavior-and-what-do-you-observe?)

* [Which cluster is affected?](#which-cluster-is-affected?)

* [Since when has the problem occurred?](#since-when-has-the-problem-occurred?)

* [How does the problem affect you?](#how-does-the-problem-affect-you?)

* [What were the last steps you did before the problem occured?](#what-were-the-last-steps-you-did-before-the-problem-occurred?)

### How can we contact you?

If you provide us with additonal contact options like a phone number we can contact you faster if we have any open questions.

### What is the desired behavior and what do you observe?

Give us a brief overview which behavior you observed in your cluster and what you expected to see.

### Which cluster is affected?

Each cluster has a unique identifier. If you don't know your identifier, you can check the second part of the name of your nodes:

```bash
$ kubectl get nodes
NAME                          STATUS    ROLES     AGE       VERSION
kubermatic-vwj2q4slrk-jrwll   Ready     <none>    13d       v1.10.2
kubermatic-vwj2q4slrk-s4j85   Ready     <none>    13d       v1.10.2
kubermatic-vwj2q4slrk-z25vc   Ready     <none>    13d       v1.10.2
```

In this example the cluster name is `vwj2q4slrk`.

### Since when has the problem occured?

It is sufficient to give us a rough estimate like

* a few minutes ago
* for about two hours
* I did just realize it does not work, but have never used this feature before

But the more specific you are, the better!

### How does the problem affect you?

Please give us a description how you found out about the problem and what you tried before contacting us.

### What were your last steps before the problem occurred?

It might speed up the debugging process if we know which services you worked on right before the interruption.

## Steps you can take in the meantime

* Please check our Notifier if we might have a known disruption of Service ongoing
* Check the official Kubernetes [Application](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/)-
  and [Cluster](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/)-Troubleshooting guides for a possible solution to your problem

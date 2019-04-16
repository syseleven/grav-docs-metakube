---
title: 'Production Grade Applications in Kubernetes'
taxonomy:
    tag:
        - kubernetes
        - deployment
        - ci
        - production
---

Kubernetes is very powerful in ensuring that your applications are scalable and stable and keep running during deployments, server outages or increased traffic. But while Kubernetes already handles a lot of scenarios on its own, there are some things you have too keep in mind for a production grade application setup on Kubernetes.

This article is meant as a starting point and check list to help you with that:

## Application design

When writing your application, you should keep in mind that it will run inside of containers on Kubernetes. Keep the principles of a [12 factor app](https://12factor.net) in mind.

## Deployments

When creating Deployments, DaemonSets, StatefulSets or CronJobs there are certain settings you should take:

### Docker Images

* Your images are as small as possible, there should be no build artifacts or unused packages, logs or caching data in your image. You can use [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) to help you with this.
* You should scan your images for known security vulnerabilities. For example with [Clair](https://github.com/coreos/clair), which is also already integrated in several docker registries.
* You should keep packages and base images up to date to prevent bugs or security vulnerabilities.
* When referencing a docker image in a Kubernetes resource like a Deployment, always reference it with a specific version and never with `latest`.

For more information see [Images](https://kubernetes.io/docs/concepts/containers/images/).

### Use namespaces for logical grouping of applications

If your application setup gets more complex with multiple different services, it is a good idea to use Namespaces to separate and group Deployments, StatefulSets, Services, ... logically per application.

For more information see [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

### Handling different environments

If you need to deploy to different environments, you should use a tool like Helm that helps you with this, instead of duplicating Kubernetes resource files.

For more information see [Helm](https://helm.sh).

### Amount of replicas

For high availability of your application and to enable deployments without downtimes you should have **at least** 2, better more, replicas of every component in your cluster. This includes stateless applications as well as databases, proxies and caching systems. Having only 1 replica will mean that it is very likely to create downtimes for this component, which your application would need to handle.

Of course you should also test that your service stays available when you delete and recreate a pod or trigger a deployment.

For more information see [Running Multiple Instances of Your App](https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/).

### Resource requests and limits

It is important to set sensible values for the CPU and memory requests and limits of a container. Requests and limits should neither be too low, nor too high.

Especially keep in mind that a too low CPU limit can mean that your container gets throttled, which could slow it down significantly and a too low memory limit could mean that it gets killed frequently.

On the other hand too low requests could mean that your worker nodes get over-committed, while too high requests could mean that you are wasting resources.

For more information see [Managing Compute Resources for Containers](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/).

### Separating workloads

Running containers on VMs inside of a Kubernetes cluster does not mean that you have a magic amount of endless resources. The amount of CPU, memory, IO and network resources are limited for each VM.

Therefore it is a good idea to deploy your production critical systems on a different set of nodes than other CPU, memory and IO intensive workloads, like monitoring and logging stacks.

You can do this with nodeSelectors, nodeAffinities, taints and tolerations.

For more information see [Assigning Pods to Nodes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) and [Taints and Tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/).

### Pod Affinities and Disruption Policies

To increase the availability of an application it may make sense to configure inter-pod affinites and anti-affinities so that Kubernetes ensures that your pods are spread across your cluster evenly.

For more information see [Inter-pod affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#inter-pod-affinity-and-anti-affinity-beta-feature).

To ensure that Kubernetes will always try to keep enough replicas of your application up and running to handle your production workload, you should define a sensible Pod Disruption Budget.

For more information see [Disruptions](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) and [Specifying a Disruption Budget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/).

### Configuration

Configuration for your applications as well as infrastructure components like webservers, language runtimes or databases should not be part of a docker image, but injected into a container by ConfigMaps and Secrets. Otherwise you would need to rebuild and complete Docker Image for every configuration change.

You should also check if the configuration of your webservices, language runtimes and databases are sensible and match the expected traffic and the available and configured memory and cpu resources in the cluster.

For example: If you are expecting a maximum of 500 concurrent requests, all replicas of your webserver combined should also have 500 worker slots and enough memory available for all of these worker slots. This should also match the settings of your language runtime and your databases. The databases should have enough memory and storage for the expected data size.

You should test this with load tests before going live.

For more information see [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

### Health Checks

You should define sensible Liveness and Readiness Probes so that Kubernetes knows if your application is healthy and running.

For more information see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).

### Lifecycle Hooks

If your application needs to perform certain tasks in order to shutdown safely, like cleaning up caches, waiting until open connections or requests are finished, you should configure Lifecycle Hooks.

For more information see [Container Lifecycle Hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/).

### Plan for Maintenance

Even in the most robust application designs there may be the need to take an application offline, either because of a planned maintenance window, or an emergency.

You should plan for these cases by having an easy way to show the users a nice, static maintenance page instead of some default nginx error.

You can do this for example by deploying a small application that just serves a static page, where you can switch a Service to, or by configuring custom fallback error pages in your ingress controller.

For more information see [Connecting Applications with Services](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/) and [Custom Errors](https://kubernetes.github.io/ingress-nginx/examples/customization/custom-errors/).

### Persistent Volumes

When creating persistent volumes, you should create them in the right size. Increasing the size of a volume is not possible while the application is running and the volume is bound to a pod.

For higher IO performance you may want to consider using local storage nodes. Since local storage volume data is not automatically spread in the OpenStack cluster, you (or your database) have to take care of replication then.

For more information see [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

### Backups

Even though persistent volumes are replicated in our OpenStack cluster, you should still configure and create backups for your stateful data.

For more information see [Backups](../07.backups/default.en.md)

## Third-Party Components

When you are deploying third-party components, like databases, an ingress controller or monitoring and logging infrastructure, best use already built Helm charts and configure them to your needs instead of re-inventing the wheel.

For more information see [Kubeapps](https://hub.kubeapps.com/).

## Security

You should think about pod security contexts. For example if possible, do not run a pod as root.

For more information see [Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

## CI/CD

Deployments should be triggered by a CI/CD pipeline that includes running unit and integration tests before a deployment and smoke tests after a deployment.

You should configure Deployment Strategies and may want to consider canary or blue/green deployments.

For more information see [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) and [Zero-downtime Deployment in Kubernetes](https://kubernetes.io/blog/2018/04/30/zero-downtime-deployment-kubernetes-jenkins/).

## Monitoring

You should monitor all your components and create sensible dashboards to quickly see their state. The [prometheus-operator](https://github.com/coreos/prometheus-operator) works quite well with Kubernetes and the [kube-prometheus](https://github.com/coreos/kube-prometheus) project helps you with deploying a Prometheus setup that already scrapes basic information about the state of Pods and Nodes inside of Kubernetes.

You should also add Prometheus exporters for all the other services and databases that you use.

For more information see [Prometheus Exporters and Integrations](https://prometheus.io/docs/instrumenting/exporters/) and [Grafana Dashboards](https://grafana.com/dashboards).

If you have multiple services in your application architecture, have a look at Service Mesh technologies like [Istio](https://istio.io/) or [LinkerD](https://linkerd.io/) that help you with setting up transparent monitoring, tracing, routing and traffic encryption between these services.

You should have external checks that validate that your application is up and running and alert you if it is not.

## Logging

Your applications should always log to `stdout` or `stderr` in a machine parsable format like JSON. Do not log to the filesystem.

### Central log management

While `kubectl logs` is quite useful, you should use a central log management system to have a longer term log storage, aggregation and full text search over your logs.

For more information see for example [elastic-stack](https://github.com/helm/charts/tree/master/stable/elastic-stack).

SysEleven also supports fully managed Elastic Stack Installations. Please contact our [Support](../../04.Support/default.en.md) for more details.

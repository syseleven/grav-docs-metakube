---
title: 'Use Horizontal Pod Autoscaling'
published: true
taxonomy:
    tag:
        - scaling
        - kubernetes
        - cli
---

Kubernetes offers Horizontal Pod Autoscaling and MetaKube clusters automatically come with the necessary metrics-server and configuration out of the box. This tutorial gives you an example on how to use this. For more generic information see the [Kubernetes Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) on it.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Deploy an application

For easy cleanups we create a new namespace for our tutorial:

```shell
$ kubectl create namespace hpa-tutorial
namespace/hpa-tutorial created
```

For our tutorial we will just deploy a NGINX Hello World app, run:

```shell
$ kubectl run hello-app --image=nginxdemos/hello --port=80 --namespace hpa-tutorial --requests="cpu=100m,memory=256Mi"
deployment.apps/hello-app created
```

Check that the pod of the new application was created successfully and is running:

```shell
$ kubectl get pods --namespace hpa-tutorial
NAME                           READY     STATUS    RESTARTS   AGE
hello-app-5c7477d7b7-n44wq     1/1       Running   0          9s
```

Once the pods are running, we expose it with a [load balancer](../13.create-a-load-balancer/default.en.md), so that we can reach it from the outside:

```shell
$ kubectl expose deployment hello-app --name=hello-app-svc --port=80 --target-port=80 --namespace=hpa-tutorial --type=LoadBalancer
service/hello-app-svc exposed
```

Check that the service received an external IP address, this can take a few seconds:

```shell
$ kubectl get service hello-app-svc --namespace=hpa-tutorial
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP       PORT(S)        AGE
hello-app-svc   LoadBalancer   10.10.10.102   195.192.xxx.xxx   80:31750/TCP   37s
```

Check that you can successfully access the service:

```shell
$ curl http://195.192.xxx.xxx/ -I
HTTP/1.1 200 OK
Server: nginx/1.13.8
Date: Mon, 27 Aug 2018 11:53:46 GMT
Content-Type: text/html
Connection: keep-alive
Expires: Mon, 27 Aug 2018 11:53:45 GMT
Cache-Control: no-cache
```

## Configure the Autoscaler

To configure the autoscaler, run:

```shell
$ kubectl autoscale deployment hello-app --namespace=hpa-tutorial --min=1 --max=6 --cpu-percent=5
horizontalpodautoscaler.autoscaling/hello-app autoscaled
```

In real life you do not want to use 5% CPU as a limit, but this way we can more easily see the effect of the autoscaler in a tutorial setting. Have a look at the official [Kubernetes Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) for more information about possible limit settings. You can check that the autoscaler was created with:

```shell
$ kubectl get horizontalpodautoscaler hello-app --namespace=hpa-tutorial
NAME        REFERENCE              TARGETS        MINPODS   MAXPODS   REPLICAS   AGE
hello-app   Deployment/hello-app   <unknown>/5%   1         6         2          37s
```

## Create load on your service

To simulate load we can use a tool like [ab](https://httpd.apache.org/docs/current/programs/ab.html) to create traffic to our service

```shell
ab -c 100 -n 1000 http://195.192.xxx.xxx/
```

After running the above command a few times (Kubernetes needs enough data in order to decide to scale up), you should see new pods being created:

```shell
$ kubectl get pods --namespace hpa-tutorial --watch
NAME                         READY     STATUS    RESTARTS   AGE
hello-app-5d6bcff5dd-frdnl   1/1       Running   0         4s
hello-app-5d6bcff5dd-bjkcj   0/1       Pending   0         0s
hello-app-5d6bcff5dd-bjkcj   0/1       Pending   0         0s
hello-app-5d6bcff5dd-948xr   0/1       Pending   0         0s
hello-app-5d6bcff5dd-mdh9n   0/1       Pending   0         0s
hello-app-5d6bcff5dd-948xr   0/1       Pending   0         1s
hello-app-5d6bcff5dd-mdh9n   0/1       Pending   0         1s
hello-app-5d6bcff5dd-bjkcj   0/1       ContainerCreating   0         1s
hello-app-5d6bcff5dd-948xr   0/1       ContainerCreating   0         1s
hello-app-5d6bcff5dd-mdh9n   0/1       ContainerCreating   0         1s
hello-app-5d6bcff5dd-bjkcj   0/1       ContainerCreating   0         3s
hello-app-5d6bcff5dd-mdh9n   0/1       ContainerCreating   0         3s
hello-app-5d6bcff5dd-948xr   0/1       ContainerCreating   0         3s
hello-app-5d6bcff5dd-bjkcj   1/1       Running   0         4s
hello-app-5d6bcff5dd-mdh9n   1/1       Running   0         7s
hello-app-5d6bcff5dd-948xr   1/1       Running   0         8s
```

For more information on the decisions the autoscaler made, you check its events:

```shell
$ kubectl describe horizontalpodautoscaler hello-app --namespace=hpa-tutorial
Name:                                                  hello-app
Namespace:                                             hpa-tutorial
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Mon, 27 Aug 2018 14:04:46 +0200
Reference:                                             Deployment/hello-app
Metrics:                                               ( current / target )
resource cpu on pods  (as a percentage of request):  0% (0) / 5%
Min replicas:                                          1
Max replicas:                                          6
Deployment pods:                                       4 current / 4 desired
Conditions:
Type            Status  Reason            Message
----            ------  ------            -------
AbleToScale     False   BackoffBoth       the time since the previous scale is still within both the downscale and upscale forbidden windows
ScalingActive   True    ValidMetricFound  the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
ScalingLimited  True    TooFewReplicas    the desired replica count is more than the maximum replica count
Events:
Type     Reason                        Age              From                       Message
----     ------                        ----             ----                       -------
Warning  FailedGetResourceMetric       2m (x2 over 3m)  horizontal-pod-autoscaler  unable to get metrics for resource cpu: no metrics returned from resource metrics API
Warning  FailedComputeMetricsReplicas  2m (x2 over 3m)  horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
Normal   SuccessfulRescale             2m               horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
```

## Clean up

Delete the namespace:

```shell
$ kubectl delete namespace hpa-tutorial
namespace "hpa-tutorial" deleted
```

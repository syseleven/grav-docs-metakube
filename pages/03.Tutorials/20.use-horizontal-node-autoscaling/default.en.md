---
title: 'Use Horizontal Node Autoscaling'
published: true
taxonomy:
    tag:
        - scaling
        - kubernetes
        - cli
---

MetaKube Kubernetes clusters support horizontal node autoscaling out of the box. This tutorial shows how
you can configure and activate it and how it plays together nicely with [horizontal pod autoscaling](../19.use-horizontal-pod-autoscaling/default.en.md).

!!! Currently the node autoscaler can only automatically create nodes, removing nodes automatically to scale a cluster down is on our [roadmap](../../01.metakube/02.roadmap/default.en.md) and will be released in the near future.

## Prerequisites

* You need to have [created a MetaKube cluster](../02.create-a-cluster/default.en.md) and [installed and configured kubectl](../07.using-kubectl/default.en.md).

## Deploy an application

For easy cleanups we create a new namespace for our tutorial:

```shell
$ kubectl create namespace hna-tutorial
namespace/hpa-tutorial created
```

For our tutorial we will just deploy an NGINX Hello World app, run:

```shell
$ kubectl run hello-app --image=nginxdemos/hello --port=80 --namespace hna-tutorial --requests="cpu=500m,memory=700Mi"
deployment.apps/hello-app created
```

Check that the pod of the new application was created successfully and is running:

```shell
$ kubectl get pods --namespace hna-tutorial
NAME                           READY     STATUS    RESTARTS   AGE
hello-app-5c7477d7b7-n44wq     1/1       Running   0          9s
```

Not that we defined fairly high CPU and memory requests for our pod. This way we will quickly get to the point where the
scheduler can not schedule new pods due to insufficient resources, if we try to scale the deployment up.

Let's now manually scale the deployment, in real life this would likely be done by the horizontal pod autoscaler:

```shell
$ kubectl scale deployment/hello-app --replicas 15 --namespace hna-tutorial
deployment.extensions/hello-app scaled
```

If you list all pods now, there should be several pods stuck in a "pending" state:

```shell
$ kubectl get pods --namespace hna-tutorial
NAME                           READY     STATUS    RESTARTS   AGE
hello-app-6f488fcdfc-6pj74   1/1     Running   0          13s
hello-app-6f488fcdfc-9bb8h   1/1     Running   0          13s
hello-app-6f488fcdfc-9bn8l   1/1     Running   0          13s
hello-app-6f488fcdfc-n44wq   1/1     Running   0          3m47s
hello-app-6f488fcdfc-dbhq9   0/1     Pending   0          13s
hello-app-6f488fcdfc-dvnsd   0/1     Pending   0          13s
hello-app-6f488fcdfc-g2m8n   0/1     Pending   0          13s
hello-app-6f488fcdfc-hm8kp   0/1     Pending   0          13s
hello-app-6f488fcdfc-kfx95   0/1     Pending   0          13s
hello-app-6f488fcdfc-m2n8x   0/1     Pending   0          13s
hello-app-6f488fcdfc-mcnbc   0/1     Pending   0          13s
hello-app-6f488fcdfc-q5fmn   0/1     Pending   0          13s
hello-app-6f488fcdfc-spk6p   0/1     Pending   0          13s
hello-app-6f488fcdfc-xr6r8   0/1     Pending   0          13s
hello-app-6f488fcdfc-zx8st   0/1     Pending   0          13s
```

If you describe one of these pending pods, you can see that the scheduling failed because of insufficient memory and CPU
resources:

```shell
kubectl describe pod hello-app-6f488fcdfc-m2n8x
Name:               hello-app-6f488fcdfc-m2n8x
Namespace:          hna-tutorial
Priority:           0
PriorityClassName:  <none>
Node:               <none>
Labels:             pod-template-hash=6f488fcdfc
                    run=hello-app
Annotations:        <none>
Status:             Pending
IP:
Controlled By:      ReplicaSet/hello-app-6f488fcdfc
Containers:
  hello-app:
    Image:      nginxdemos/hello
    Port:       80/TCP
    Host Port:  0/TCP
    Requests:
      cpu:        500m
      memory:     700Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-6d8qc (ro)
Conditions:
  Type           Status
  PodScheduled   False
Volumes:
  default-token-6d8qc:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-6d8qc
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason            Age                 From                Message
  ----     ------            ----                ----                -------
  Warning  FailedScheduling  36s (x25 over 81s)  default-scheduler   0/5 nodes are available: 1 Insufficient memory, 5 Insufficient cpu.
```

This means we need to increase the capacity of our cluster by adding more nodes. Of course this could be done manually through
the MetaKube dashboard (see [Adding a worker node](../08.add-a-worker-node/default.en.md)) or over CLI (see [Managing worker nodes over CLI](../11.manage-worker-nodes-via-cli/default.en.md)),
but for this tutorial, let's use the cluster autoscaler.

For this we have to create a scalable MachineDeployment in our cluster:

```bash
# Choose the public key you want to deploy on the node
SSH_PUBLIC_KEY=$(cat ~/.ssh/id_rsa.pub)

# Getting the cluster name like this only works if you did not rename the
# context in the downloaded kubeconfig. If you did, choose the original name of
# the cluster (10 character alphanumeric string).
CLUSTER_NAME=$(kubectl config current-context)

# Set up the flavor for the node
FLAVOR="m1.micro"

OPERATING_SYSTEM="ubuntu"
IMAGE_NAME="Ubuntu 18.04 LTS sys11 optimized - 2018-08-13"
REGION="dbl"
AVAILABILITY_ZONE="dbl1"
FLOATING_IP_POOL="ext-net"
K8S_VERSION="1.12.2"

cat <<EOF | kubectl apply -f -
apiVersion: cluster.k8s.io/v1alpha1
kind: MachineDeployment
metadata:
  annotations:
    cluster-autoscaler/min-size: "0"
    cluster-autoscaler/max-size: "15"
  name: scalable-machine-deployment
  namespace: kube-system
spec:
  replicas: 0
  minReadySeconds: 0
  selector:
    matchLabels:
      deployment: scalable-machine-deployment
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
  paused: false
  template:
    metadata:
      labels:
        deployment: scalable-machine-deployment
    spec:
      providerConfig:
        value:
          cloudProvider: openstack
          cloudProviderSpec:
            availabilityZone: ${AVAILABILITY_ZONE}
            domainName: ""
            flavor: ${FLAVOR}
            floatingIpPool: ${FLOATING_IP_POOL}
            identityEndpoint: "https://api.${REGION}.cloud.syseleven.net:5000/v3"
            image: "${IMAGE_NAME}"
            network: kubermatic-${CLUSTER_NAME}
            password: ""
            region: ${REGION}
            securityGroups:
            - kubermatic-${CLUSTER_NAME}
            tenantName: ""
            tokenId: ""
            username: ""
          operatingSystem: ${OPERATING_SYSTEM}
          operatingSystemSpec:
            distUpgradeOnBoot: false
          sshPublicKeys:
          - "${SSH_PUBLIC_KEY}"
      versions:
        kubelet: "${K8S_VERSION}"
EOF
```

This will create a MachineDeployment that initially contains 0 machines, but can be scaled up by the cluster autoscaler to a maximum
of 15 machines, if you have enough quota of course. For every machine a VM will be automatically created in OpenStack, provisioned and joined as a node to the
Kubernetes cluster.

For more details on MachineDeployments see [Cluster Management API](../../02.Documentation/11.cluster-api/default.en.md).

If you list all available machines, you can see that a few new machines have been automatically created by the autoscaler:

```shell
$ kubectl get machines -n kube-system
NAME                                           AGE
machine-kubermatic-fhgbvx65xg-7flj7            8d
machine-kubermatic-fhgbvx65xg-hmgd4            8d
machine-kubermatic-fhgbvx65xg-q287t            8d
scalable-machine-deployment-5c4cbbc47b-62wsd   4m
scalable-machine-deployment-5c4cbbc47b-zwrts   4m
```

And the replica count of the MachineDeployment has been updated:

```shell
$ kubectl get machinedeployment -n kube-system scalable-machine-deployment -o jsonpath="{.spec.replicas}"
2
```

After a few minutes, once the VMs are started and provisioned, new nodes will appear in the cluster as well:

```shell
$ kubectl get nodes
NAME                                           STATUS     ROLES    AGE     VERSION
kubermatic-fhgbvx65xg-7flj7                    Ready      <none>   8d      v1.12.2
kubermatic-fhgbvx65xg-hmgd4                    Ready      <none>   8d      v1.12.2
kubermatic-fhgbvx65xg-q287t                    Ready      <none>   8d      v1.12.2
scalable-machine-deployment-5c4cbbc47b-62wsd   Ready      <none>   2m58s   v1.12.2
scalable-machine-deployment-5c4cbbc47b-zwrts   Ready      <none>   2m30s   v1.12.2
```

And the previously pending pods are now successfully scheduled and running:

```shell
$ kubectl get pods --namespace hna-tutorial
NAME                           READY     STATUS    RESTARTS   AGE
hello-app-6f488fcdfc-6pj74   1/1     Running   0          13s
hello-app-6f488fcdfc-9bb8h   1/1     Running   0          13s
hello-app-6f488fcdfc-9bn8l   1/1     Running   0          13s
hello-app-6f488fcdfc-n44wq   1/1     Running   0          3m47s
hello-app-6f488fcdfc-dbhq9   1/1     Running   0          13s
hello-app-6f488fcdfc-dvnsd   1/1     Running   0          13s
hello-app-6f488fcdfc-g2m8n   1/1     Running   0          13s
hello-app-6f488fcdfc-hm8kp   1/1     Running   0          13s
hello-app-6f488fcdfc-kfx95   1/1     Running   0          13s
hello-app-6f488fcdfc-m2n8x   1/1     Running   0          13s
hello-app-6f488fcdfc-mcnbc   1/1     Running   0          13s
hello-app-6f488fcdfc-q5fmn   1/1     Running   0          13s
hello-app-6f488fcdfc-spk6p   1/1     Running   0          13s
hello-app-6f488fcdfc-xr6r8   1/1     Running   0          13s
hello-app-6f488fcdfc-zx8st   1/1     Running   0          13s
```

## Clean up

Delete the MachineDeployment to remove the created machines and VMs:

```shell
$ kubectl delete machinedeployment -n kube-system scalable-machine-deployment
machinedeployment.cluster.k8s.io "scalable-machine-deployment" deleted
```

Delete the namespace:

```shell
$ kubectl delete namespace hna-tutorial
namespace "hna-tutorial" deleted
```

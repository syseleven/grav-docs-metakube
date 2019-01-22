---
title: 'Manage worker nodes via CLI'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - metakube
        - cli
        - node
---

Worker nodes can be managed with our web UI as described in [add a worker node](../08.add-a-worker-node/default.en.md). Once you installed [kubectl](../07.using-kubectl/default.en.md), you can also manage them via Command-Line-Interface (CLI) in order to automate creation, deletion and upgrades of nodes.

## List all available nodes

To get a list of all nodes execute:

```bash
kubectl get nodes -o wide
```

Every node is managed by a machine resource in the `kube-system` namespace. To list all machine resources, execute:

```bash
kubectl get machines --namespace kube-system
```

## Add a node

To add a node via CLI, set up the following variables:

```bash
# Set up the flavor for the node
FLAVOR="m1.micro"

# Choose the public key, you want to deploy on the node
SSH_PUBLIC_KEY=$(cat ~/.ssh/id_rsa.pub)

# Getting the cluster name like this only works if you did not rename the
# context in the downloaded kubeconfig. If you did, choose the original name of
# the cluster (10 character alphanumeric string).
CLUSTER_NAME=$(kubectl config current-context)
```

Afterwards run the following commands. You most likely do not need to change any of the remaining variables:

```bash
OPERATING_SYSTEM="ubuntu"
IMAGE_NAME="Ubuntu 18.04 LTS sys11 optimized - 2018-08-13"
REGION="dbl"
AVAILABILITY_ZONE="dbl1"
FLOATING_IP_POOL="ext-net"
K8S_VERSION="1.12.2"
MACHINE_NAME=$(cat /dev/urandom | base64 | tr -cd 'a-z0-9' | head -c 5)

cat <<EOF | kubectl apply -f -
apiVersion: cluster.k8s.io/v1alpha1
kind: Machine
metadata:
  name: machine-kubermatic-${CLUSTER_NAME}-${MACHINE_NAME}
  namespace: kube-system
spec:
  metadata:
    name: kubermatic-${CLUSTER_NAME}-${MACHINE_NAME}
  providerConfig:
    value:
        cloudProvider: openstack
        cloudProviderSpec:
          availabilityZone: ${AVAILABILITY_ZONE}
          flavor: ${FLAVOR}
          floatingIpPool: ${FLOATING_IP_POOL}
          identityEndpoint: "https://api.${REGION}.cloud.syseleven.net:5000/v3"
          image: "${IMAGE_NAME}"
          network: kubermatic-${CLUSTER_NAME}
          region: ${REGION}
          securityGroups:
          - kubermatic-${CLUSTER_NAME}
        operatingSystem: ${OPERATING_SYSTEM}
        operatingSystemSpec:
          distUpgradeOnBoot: false
        sshPublicKeys:
        - "${SSH_PUBLIC_KEY}"
  roles:
  - Node
  versions:
    kubelet: "v${K8S_VERSION}"
EOF
```

For more details on Machines see [Cluster Management API](../../02.Documentation/12.cluster-api/default.en.md).

## Delete a node

Find the machine name of the node you want to delete (see _Listing all available nodes_) and execute:

```bash
kubectl delete machine machine-kubermatic-name --namespace kube-system
```

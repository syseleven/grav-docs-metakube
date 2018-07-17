---
title: 'Manage worker nodes via CLI'
published: false
date: '17-07-2018 16:50'
publish_date: '01-08-2018 12:00'
taxonomy:
    tag:
        - metakube
        - cli
        - node
---

Worker nodes can be managed with our Web UI as described in [add a worker node](/tutorials/add-a-worker-node). Once you installed [kubectl](/tutorials/using-kubectl), you can also manage them via Command-Line-Interface in order to automate creation, deletion and upgrades of nodes.

## Listing all available nodes

To see a list of all nodes execute:

```bash
kubectl get nodes -o wide
```

Every node is managed by a machine resource, to list all machine resources, execute

```bash
kubectl get machines
```

## Adding a node

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

Afterwards run the following commands. You most likely do not need to change any
of the remaining variables:

```bash
OPERATING_SYSTEM="ubuntu"
IMAGE_NAME="Ubuntu 16.04 LTS 2018.03.26"
REGION="dbl"
AVAILABILITY_ZONE="dbl1"
FLOATING_IP_POOL="ext-net"
K8S_VERSION="1.10.3"
DOCKER_VERSION="17.03.2"
MACHINE_NAME=$(cat /dev/urandom | base64 | tr -cd 'a-z0-9' | head -c 5)

cat <<EOF | kubectl apply -f -
apiVersion: machine.k8s.io/v1alpha1
kind: Machine
metadata:
  name: machine-kubermatic-${CLUSTER_NAME}-${MACHINE_NAME}
spec:
  metadata:
    name: kubermatic-${CLUSTER_NAME}-${MACHINE_NAME}
  providerConfig:
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
    containerRuntime:
      name: docker
      version: ${DOCKER_VERSION}
    kubelet: "v${K8S_VERSION}"
EOF
```

## Deleting a node

Find the machine name of the node you want to delete (see _Listing all available nodes_) and execute

```bash
kubectl delete machine machine-kubermatic-name
```
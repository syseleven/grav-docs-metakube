---
title: 'DNS'
taxonomy:
    tag:
        - metakube
        - dns
        - coredns
        - dns-cache
        - nodelocal-dns-cache
---

Kubernetes has its own internal DNS service to provide service discovery between pods and services inside of the cluster. You can find more information about this at [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/).

## CoreDNS

In MetaKube clusters we provide a highly available installation of [CoreNDS](https://coredns.io/) as a cluster internal DNS solution.

## Nodelocal DNS Cache

Due to the way service discovery in Kubernetes works, the internal DNS resolution is a critical part of the cluster. To increase DNS performance and prevent DNS timeouts within the cluster we also provide a node-local-dns cache pod on every cluster node. These pods also run CoreDNS as the DNS cache. It works by running with `hostNetwork:true` and creating a dedicated dummy interface with a link local IP (`169.254.20.10/32` by default) to listen for DNS queries. The cache instances connect to the cluster wide DNS service in case of cache misses.

For more information also have a look at the [Nodelocal DNS Cache GitHub Page](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/dns/nodelocaldns).

## Configure Metakube DNS servers

Metakube allows the user to add additional configurations to CoreDNS and NodeLocal DNS Cache. This is achieved by creating a config map as follows:

### CoreDNS
Create a configmap witht the name `coredns-extra-configs`, containing a valid CoreDNS style [configuration file](https://coredns.io/manual/toc/#configuration).
The configuration file name must be `Corefile` or end in `Corefile` as shown below.
```bash
cat <<'EOF' | kubectl apply --namespace kube-system -f -
apiVersion: v1
data:
  Corefile: |
    <Add zone information> {
      <Add additional DNS configurations here>   
    }
kind: ConfigMap
metadata:
  name: coredns-extra-configs
  namespace: kube-system
EOF
```

Once the configmap has been created, use the following code block to restart pods to load the newly added configuration to CoreDNS

```bash
kubectl scale deployment.v1.apps/coredns --replicas=0 -n kube-system
sleep 5
kubectl scale deployment.v1.apps/coredns --replicas=2 -n kube-system
```

### NodeLocal DNS cache
Create a configmap witht the name `node-local-dns-extra-configs`, containing a valid CoreDNS style [configuration file](https://coredns.io/manual/toc/#configuration).
The configuration file name must be `Corefile` or end in `Corefile` as shown below.
```bash
cat <<'EOF' | kubectl apply --namespace kube-system -f -
apiVersion: v1
data:
  Corefile: |
    <Add zone information> {
      <Add additional DNS configurations here>   
    }
kind: ConfigMap
metadata:
  name: node-local-dns-extra-configs
  namespace: kube-system
EOF
```

Once the configmap has been created, use the following code block to restart pods to load the newly added configuration to CoreDNS

```bash
kubectl -n kube-system patch daemonset node-local-dns -p '{"spec": {"template": {"spec": {"nodeSelector": {"non-existing": "true"}}}}}'
sleep 5
kubectl -n kube-system patch daemonset node-local-dns --type json -p='[{"op": "remove", "path": "/spec/template/spec/nodeSelector/non-existing"}]'
```

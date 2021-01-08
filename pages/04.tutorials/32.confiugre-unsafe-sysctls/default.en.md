---
title: 'How to configure unsafe sysctls'
published: true
date: '07-01-2021 21:30'
taxonomy:
    tag:
        - metakube
        - cluster
        - sysctl
        - kubelet
        - securityContext
---

At some point during your Kubernetes journey you will come to the point where you need to adjust some parameters of the underlying node system, either globally or in the context of a specific pod or set of pods. Maybe you are running Redis and need to [increase the amount of allowed maximum connections](https://github.com/redis/redis/blob/unstable/redis.conf#L100) on a socket or you use Apache webserver and want to [enable TCP Keepalive](http://httpd.apache.org/docs/current/mod/core.html#keepalive) but with different default parameters than the kernel compiled ones.

To do so you need to adjust the according sysctl values. By default your Kubernetes installation [will prevent you from doing so](https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster/) to ensure a secure and stable workload environment. If you try anyway e.g., by specifying:

```yaml
securityContext:
  sysctls:
  - name: "net.ipv4.tcp_keepalive_time"
    value: "45"
  - name: "net.ipv4.tcp_keepalive_intvl"
    value: "45"
  - name: "net.ipv4.tcp_keepalive_probes"
    value: "10"
```

chances are you will see something like this in the event log output for your pods:

```bash
kubectl describe pod/test

...
Events:
  Type     Reason           Age   From               Message
  ----     ------           ----  ----               -------
  Normal   Scheduled        2s    default-scheduler  Successfully assigned test/test to metakube-worker-87wh9-85b5db8db7-q6x7t
  Warning  SysctlForbidden  2s    kubelet            forbidden sysctl: "net.ipv4.tcp_keepalive_time" not whitelisted
```

If however you know what you are doing and are aware about the side effects of your changes, you can allow non privileged pods to use those sysctls.

## Requirements

* A node deployment that has [enabled the dynamic kubelet](../../02.documentation/20.dynamic-kubelet-config/default.en.md)
* Write Access to the kubelet configmaps in the kube-system namespace

## Adjusting the kubelet configuration

If you have enabled the dynamic kubelet feature you should find a `kubelet-config-1.X` in your kube-system namespace:

```bash
kubectl get configmap --namespace kube-system --selector kubermatic-addon=kubelet-configmap

NAME                  DATA   AGE
kubelet-config-1.11   1      1d
kubelet-config-1.12   1      1d
kubelet-config-1.13   1      1d
kubelet-config-1.14   1      1d
kubelet-config-1.15   1      1d
kubelet-config-1.16   1      1d
kubelet-config-1.17   1      1d
kubelet-config-1.18   1      1d
```

In order to allow your pods to set your required sysctl values you first need to adjust the configmap corresponding to your current cluster version. You can determine it by running e.g.:

```bash
kubectl version --short

Client Version: v1.20.1
Server Version: v1.18.13
```

Edit the configmap resource and add something like this (e.g., if you want to tweak the tcp keepalive default values for your pods) below the line that contains `kubelet: |`:

```yaml
allowedUnsafeSysctls:
  - "net.ipv4.tcp_keepalive_time"
  - "net.ipv4.tcp_keepalive_intvl"
  - "net.ipv4.tcp_keepalive_probes"
```

Your configmap now should look somewhat like this:

```yaml
apiVersion: v1
data:
  kubelet: |
    address: 0.0.0.0
    apiVersion: kubelet.config.k8s.io/v1beta1
    allowedUnsafeSysctls:
      - "net.ipv4.tcp_keepalive_time"
      - "net.ipv4.tcp_keepalive_intvl"
      - "net.ipv4.tcp_keepalive_probes"
    authentication:
      anonymous:
        enabled: false
      webhook:
        cacheTTL: 2m0s
        enabled: true
      x509:
        clientCAFile: /etc/kubernetes/pki/ca.crt
    authorization:
    ...
```

After you have applied the new configmap (or saved it if you used `kubectl edit`) you can now verify that the kubelet has in fact loaded the new configuration by running the following two commands:

```bash
kubectl proxy --port=8001 &
NODE_NAME="$(kubectl get nodes --output jsonpath='{ .items[0].metadata.name }')"
curl --silent --show-error --location "http://localhost:8001/api/v1/nodes/${NODE_NAME}/proxy/configz"
```

You should see a blob of JSON that contains your new options e.g., `"allowedUnsafeSysctls":["net.ipv4.tcp_keepalive_time","net.ipv4.tcp_keepalive_intvl","net.ipv4.tcp_keepalive_probes"]`.  If you want to know how this works, have a [look at the official kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/reconfigure-kubelet/#generate-the-configuration-file)

Now you are able to create pods that use those sysctls inside their securityContext options e.g.:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  securityContext:
    sysctls:
    - name: "net.ipv4.tcp_keepalive_time"
      value: "45"
    - name: "net.ipv4.tcp_keepalive_intvl"
      value: "45"
    - name: "net.ipv4.tcp_keepalive_probes"
      value: "10"
  containers:
  - args:
    - sleep
    - "86400"
    image: alpine
    name: test
```

Again you can verify that this works i.e.:

```bash
kubectl exec --stdin --tty test -- sysctl net.ipv4.tcp_keepalive_time net.ipv4.tcp_keepalive_intvl net.ipv4.tcp_keepalive_probes

net.ipv4.tcp_keepalive_time = 45
net.ipv4.tcp_keepalive_intvl = 45
net.ipv4.tcp_keepalive_probes = 10
```

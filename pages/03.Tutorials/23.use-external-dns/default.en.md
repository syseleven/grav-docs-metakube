---
title: 'Setting up externalDNS'
published: true
taxonomy:
    tag:
        - kubernetes
        - nameserver
---

If you don't want to manage DNS manually you can configure your MetaKube cluster to use [external DNS](https://github.com/kubernetes-incubator/external-dns) with an compatible DNS provider (e.g. Amazon Route53). In this tutorial we will use [Amazon Route53](https://aws.amazon.com/de/route53/). For other external DNS providers please check the [official docs](https://github.com/kubernetes-incubator/external-dns/tree/master/docs/tutorials).

## Prepare the nodes

*This step is not required, if you cluster is running on AWS.*

!! TODO !!

## Deploy the external DNS service

For easier cleanup we will create a dedicated namespace for the external DNS service

```
$ kubectl create namespace external-dns
namespace/external-dns created
```

and deploy the externalDNS service into this namespace

```
$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: external-dns
  namespace: external-dns

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: external-dns
rules:
- apiGroups: [""]
  resources: ["services"]
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","watch","list"]
- apiGroups: ["extensions"]
  resources: ["ingresses"]
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: external-dns-viewer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: external-dns
subjects:
- kind: ServiceAccount
  name: external-dns
  namespace: external-dns

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: external-dns
  namespace: external-dns
spec:
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: external-dns
    spec:
      serviceAccountName: external-dns
      containers:
      - name: external-dns
        image: registry.opensource.zalan.do/teapot/external-dns:latest
        args:
        - --source=service
        - --source=ingress
        - --domain-filter=<example.com>
        - --provider=aws
        - --policy=upsert-only # would prevent ExternalDNS from deleting any records, omit to enable full synchronization
        - --aws-zone-type=public
        - --registry=txt
        - --txt-owner-id=/hostedzone/<aws_zone_id>
EOF
```

For `<example.com>` enter your hosted zone. The `<aws_zone_id>` can be found in the Route53 dashboard.

## Test the external DNS provider

When the provider was successfully deployed, you can create new LoadBalancer services for which the DNS entrys will be deployed automatically. 

```
$ cat <<EOF | kubectl apply --namespace=external-dns -f -
apiVersion: v1
kind: Service
metadata:
  name: nginx
  annotations:
    external-dns.alpha.kubernetes.io/hostname: <loadbalancer.example.com>
spec:
  type: LoadBalancer
  ports:
  - port: 80
    name: http
    targetPort: 80
  selector:
    app: nginx

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
          name: http
EOF
```

When the loadbalancer was successfully created, you should be able to visit the domain `<loadbalancer.example.com>` after a short time.

```
$ curl -I <loadbalancer.example.com>
HTTP/1 200
[...]
```

This also works seamlessly with Ingress resources. To test this 

```
$ cat <<EOF | kubectl apply --namespace=external-dns -f -
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress
spec:
  ports:
  - port: 80
    name: http
    targetPort: 80
  selector:
    app: nginx

---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: external-dns
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: <ingress.example.com>
    http:
      paths:
      - backend:
          serviceName: nginx-ingress
          servicePort: 80
```

and visit the page `<ingress.example.com>`.

## Cleanup

If you do not plan to further use the external DNS provider, just delete the `external-dns` namespace

```
$ kubectl delete namesapce external-dns
```

If you did not remove the config option `--policy=upsert-only` from the external-dns deployment you also need to delete the DNS entries `<loadbalancer.example.com>` and `<ingress.example.com>` from Route53.

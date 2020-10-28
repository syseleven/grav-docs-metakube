---
title: 'Setting up externalDNS'
published: true
taxonomy:
    tag:
        - kubernetes
        - nameserver
---

To eliminate the burden of having to manage your DNS zone manually. You may use [external DNS](https://github.com/kubernetes-incubator/external-dns) with your MetaKube cluster and a compatible DNS provider (e.g. Amazon Route53, OpenStack Designate etc.). In this tutorial we will demo how to setup and use ExternalDNS with both [Amazon Route53](https://aws.amazon.com/de/route53/) and [OpenStack Designate](https://docs.openstack.org/designate/latest/). For other external DNS providers please check the [official docs](https://github.com/kubernetes-incubator/external-dns/tree/master/docs/tutorials).

## Setting up ExternalDNS for Services on OpenStack Designate

This example describes how to setup ExternalDNS for usage within a Kubernetes cluster using OpenStack Designate DNS.

### Creating DNS zones

Before setting up ExternalDNS you will need to setup [API access](https://docs.syseleven.de/syseleven-stack/de/tutorials/api-access) to OpenStack and create a DNS Zone.

All domain names that ExternalDNS is going to create must belong to one of DNS zones created in advance. Here is an example of how to create `example.com` DNS zone:

```console
openstack zone create --email dnsmaster@example.com example.com.
```

It is important to manually create all the zones that are going to be used for kubernetes entities (ExternalDNS sources) before starting ExternalDNS.

For more details on how to use Designate follow our [documentation](https://docs.syseleven.de/syseleven-stack/de/tutorials/dnsaas)

### Create a values.yaml file to configure OpenStack Designate as external DNS provider

For easy deployment use our fully managed [External DNS Add-on](../../03.addons/09.metakube-external-dns/default.en.md)

For manual deployment of ExternalDNS copy the following text block into the file `values.yaml`. We need this file to pass our DNS configuration to the helm chart. Please make sure to check all of the fields with the place holder "changeme". You will need to add your OpenStack credentials.

```yaml
## Use Syseleven hosted image for designate to work properly until upstream PR for record update bug (https://github.com/kubernetes-incubator/external-dns/pull/1136)
## is merged.
image:
  name: syseleven/external-dns
  tag: v0.5.15-1-g810bbeaa

## This controls which types of resource external-dns should 'watch' for new
## DNS entries.
sources:
  - service
  - ingress

## The DNS provider where the DNS records will be created (options: aws, google, inmemory, azure, rfc2136 )
provider: designate

# OpenStack credentials are set through environment variables
extraEnv:
  - name: OS_PROJECT_NAME
    value: <changeme>
  - name: OS_USER_DOMAIN_NAME
    value: <changeme>
  - name: OS_USERNAME
    value: <changeme>
  - name: OS_PASSWORD
    value: <changeme>
  - name: OS_AUTH_URL
    value: <changeme>
  - name: OS_REGION_NAME
    value: <changeme>

## Limit possible target zones by domain suffixes (optional)
domainFilters: []
## Limit possible target zones by zone id (optional)
zoneIdFilters: []
# Filter sources managed by external-dns via annotation using label selector semantics (default: all sources)
annotationFilter: ""
## Adjust the interval for DNS updates
interval: "1m"

# Registry to use for ownership (txt or noop)
registry: "txt"

# When using the TXT registry, a name that identifies this instance of ExternalDNS
txtOwnerId: "changeme"

## Modify how DNS records are sychronized between sources and providers (options: sync, upsert-only )
policy: upsert-only

# Verbosity of the logs (options: panic, debug, info, warn, error, fatal)
logLevel: info

## CPU and Memory limit and request for external-dns
resources:
  limits:
    memory: 50Mi
  requests:
    memory: 50Mi
    cpu: 10m

rbac:
  create: true
 ```

## Setting up ExternalDNS for Services on AWS

### Add a new IAM user to your AWS account

Login to your AWS account and create a user named external-dns. You can find useful help on how to accomplish this here:

[AWS Create IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

### Add this IAM Policy to your external-dns user

You can find additional information about attaching policies to IAM users here:

[AWS attach policy to IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)


```json
{
 "Version": "2012-10-17",
 "Statement": [
   {
     "Effect": "Allow",
     "Action": [
       "route53:ChangeResourceRecordSets"
     ],
     "Resource": [
       "arn:aws:route53:::hostedzone/*"
     ]
   },
   {
     "Effect": "Allow",
     "Action": [
       "route53:ListHostedZones",
       "route53:ListResourceRecordSets"
     ],
     "Resource": [
       "*"
     ]
   }
 ]
}
```

### Create a values.yaml file to configure Route53 as external DNS provider

For easy deployment use our fully managed [External DNS Add-on](https://docs.syseleven.de/metakube/de/addons/metakube-external-dns)

For manual deployment of ExternalDNS copy the following text block into the file `values.yaml`. We need this file to pass our DNS configuration to the helm chart. Please make sure to check all of the fields with the place holder "changeme". You will need to add your access key and the secret key of the user you created earlier.

```yaml
## External DNS
image:
  name: registry.opensource.zalan.do/teapot/external-dns
  tag: v0.5.14

## This controls which types of resource external-dns should 'watch' for new
## DNS entries.
sources:
  - service
  - ingress

## The DNS provider where the DNS records will be created (options: aws, google, inmemory, azure, rfc2136 )
provider: aws

# AWS Access keys to inject as environment variables
aws:
  secretKey: "changeme"
  accessKey: "changeme"
  # pre external-dns 0.5.9 home dir should be `/root/.aws`
  credentialsPath: "/.aws"
  roleArn: ""
  region: "eu-central-1"
  # Filter for zones of this type (optional, options: public, private)
  zoneType: "public"

## Limit possible target zones by domain suffixes (optional)
domainFilters: []
## Limit possible target zones by zone id (optional)
zoneIdFilters: []
# Filter sources managed by external-dns via annotation using label selector semantics (default: all sources)
annotationFilter: ""
## Adjust the interval for DNS updates
interval: "1m"

# Registry to use for ownership (txt or noop)
registry: "txt"

# When using the TXT registry, a name that identifies this instance of ExternalDNS
txtOwnerId: "changeme"

## Modify how DNS records are sychronized between sources and providers (options: sync, upsert-only )
policy: upsert-only

# Verbosity of the logs (options: panic, debug, info, warn, error, fatal)
logLevel: info

## CPU and Memory limit and request for external-dns
resources:
  limits:
    memory: 50Mi
  requests:
    memory: 50Mi
    cpu: 10m

rbac:
  create: true
 ```

## Deploy the external DNS service with helm

Update your helm repositories

```shell
helm repo update
```

Install the external dns helm chart in the external-dns namespace

```shell
helm upgrade --install external-dns --namespace=external-dns -f values.yaml stable/external-dns
```

## Test the external DNS provider

When the provider is successfully deployed, you can create a new LoadBalancer service the DNS entries will be created automatically for the domain configured with the `external-dns.alpha.kubernetes.io/hostname` annotation.

```shell
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
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
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

```shell
$ curl -I <loadbalancer.example.com>
HTTP/1 200
[...]
```

This also works seamlessly with Ingress resources. To test this

```shell
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
apiVersion: networking.k8s.io/v1beta1
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

If you do not plan to continue using the external DNS provider, just delete the `external-dns` namespace

```shell
$ kubectl delete namesapce external-dns
namespace "external-dns" deleted
```

If the `policy` config option from the external-dns helm chart above is set to `upsert-only`, you also need to delete the DNS entries `<loadbalancer.example.com>` and `<ingress.example.com>` from you hosted zone on Route53.

---
title: 'Redis'
published: true
taxonomy:
    tag:
        - metakube
        - addon
        - redis
        - key-value
        - cache
        - message
        - broker
---

[Redis](https://www.vaultproject.io/) is a distributed in-memory key-value database that also provides a cache and a message broker implementation.

You can find more information about Redis here:

* [Redis documentation](https://redis.io/documentation)

## Installation

For information on how to install and uninstall an Add-On see [MetaKube Add-Ons](../default.en.md).

## Customization

The following customization options are possible for Vault:

| Option | Description | Default value |
| ------ | ----------- | ------------- |
| High availability | Provides a redis cluster in more that one instance | false |
| CPU Requests | How many milli CPUs should be reserved | 100m |
| Memory Requests | How much memory should be reserved | 128Mi |
| CPU Limits | What is the maximum of used milli CPUs | 200m |
| Memory Limits | What is the maximum of used memory by the pod | 512Mi |
| Disk Size | What is the size of the Persistent Volume provided to Redis | 10Gi |
| Redis Max Memory | What is the maximum amount of Memory allocated by Redis | 512Mi |

It is recommended to set `Memory Limits` and `Disk Size` to an amount 20% higher
than your intended `Redis Memory Size`.

If `High Availability` is set to `true`, a cluster with 3 Redis instances is provided.

## Add-on specification

If redis has high-availability set to `true`, communication with the database is provided via [ha-proxy](https://www.haproxy.org/),
it enhances communication speed from outside the cluster. A redis [sentinel](https://redis.io/topics/sentinel) is also provided for high-availability purposes.


The redis service is provided only within the cluster by default with the service names `syseleven-redis-redis-ha.syseleven-redis`
if High Availability is off; and`syseleven-redis-redis-ha-haproxy.syseleven-redis` if High Availability is on.

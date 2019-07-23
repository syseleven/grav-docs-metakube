---
title: 'Cluster Monitoring Stack'
published: false
date: '18-07-2019 13:00'
taxonomy:
    tag:
        - metakube
        - monitoring
        - prometheus
        - grafana
---

The monitoring addon installs a complete monitoring and alerting stack which enables monitoring the cluster as well as all workloads in it.

It automatically comes with sensible, generic dashboards and alerting rules for Kubernetes clusters, and can be easily extended with custom metric sources, dashboards and alerting rules.

## Installed applications

The following applications are installed:

### prometheus-operator

The prometheus-operator orchestrates the management and configuration of the whole monitoring stack. It also provides ways to configure it with Kubernetes resources.

* [Documentation](https://github.com/coreos/prometheus-operator)

### Prometheus

Prometheus is a powerful open-source systems monitoring and alerting toolkit, that integrates nicely with Kubernetes.

* [Documentation](https://prometheus.io/docs/)

### AlertManager

AlertManager enables sending out alerts based on Prometheus metrics and queries.

* [Documentation](https://prometheus.io/docs/alerting/alertmanager/)

### kube-state-metrics

kube-state-metrics is a monitoring agent that generates and exposes cluster-level metrics of the Kubernetes cluster to Prometheus.

* [Documentation](https://github.com/kubernetes/kube-state-metrics)

### Grafana

Grafana is a powerful data visualization tool with which you can create rich dashboards from your Prometheus metrics.

## Customization

The addon allows you to customize resource usage and data retention time for the applications installed as part of the addon:
![addon configurations](monitoring_addon_configs_01.png)

### Scraping additional applications with Prometheus


### Adding alerting rules

### Configuring where AlertManager should send alerts

### Adding Grafana dashboards

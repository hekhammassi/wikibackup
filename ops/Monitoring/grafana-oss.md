---
title: grafana-oss
description: 
published: true
date: 2024-11-07T09:21:17.777Z
tags: 
editor: markdown
dateCreated: 2024-11-07T09:21:17.777Z
---

# Header
## step 01 : install grafana oss , prometheus and grafana loki on server 

> version: "3"
> 
> networks:
>   loki:
> 
> services:
>   loki:
>     image: grafana/loki:2.9.2
>     ports:
>       - "3100:3100"
>     command: -config.file=/etc/loki/local-config.yaml
>     networks:
>       - loki
> 
>   promtail:
>     image: grafana/promtail:2.9.2
>     volumes:
>       - /var/log:/var/log
>     command: -config.file=/etc/promtail/config.yml
>     networks:
>       - loki
>   prometheus-container:
>     privileged: true
>     image: prom/prometheus
>     container_name: prometheus
>     ports:
>       - "9090:9090"
>     volumes:
>       - /opt/prometheus.yml:/etc/prometheus/prometheus.yml
>       - prometheus-data:/prometheus
>       - ./grafana/alerts/prometheus_rules.yaml:/etc/prometheus/rules.yaml
>       - ./grafana/alerts/prometheus_alerts.yaml:/etc/prometheus/alerts.yaml
>     command:
>       - '--config.file=/etc/prometheus/prometheus.yml'
>       - '--web.enable-remote-write-receiver'
>   grafana:
>     environment:
>       - GF_PATHS_PROVISIONING=/etc/grafana/provisioning
>       - GF_AUTH_ANONYMOUS_ENABLED=true
>       - GF_AUTH_ANONYMOUS_ORG_ROLE=Admin
>     entrypoint:
>       - sh
>       - -euc
>       - |
>         mkdir -p /etc/grafana/provisioning/datasources
>         cat <<EOF > /etc/grafana/provisioning/datasources/ds.yaml
>         apiVersion: 1
>         datasources:
>         - name: Loki
>           type: loki
>           access: proxy 
>           orgId: 1
>           url: http://loki:3100
>           basicAuth: false
>           isDefault: true
>           version: 1
>           editable: false
>         EOF
>         /run.sh
>     image: grafana/grafana:latest
>     ports:
>       -  "3000:3000"
>     networks:
>       - loki
>     volumes:
>       - grafana-storage:/var/lib/grafana
>      # - ./grafana/alerts:/etc/grafana/provisioning/alerting
> volumes:
>   prometheus-data:
>   grafana-storage:


## step 02 : install grafana alloy 
https://grafana.com/docs/alloy/latest/set-up/install/kubernetes/

## step 03 : install node exporters 
https://artifacthub.io/packages/helm/prometheus-community/prometheus-node-exporter
## step 04 : configure grafana alloy config maps 

> prometheus.remote_write "metrics" {
>   endpoint {
>     url = "http://4.178.138.23:9090/api/v1/write"
>   }
> }
> discovery.kubernetes "k8smetrics" {
>   role = "pod"
> }
> prometheus.scrape "prometrics" {
>   targets    = discovery.kubernetes.k8smetrics.targets
>   forward_to = [prometheus.remote_write.metrics.receiver]
> }
> logging {
>   level  = "info"
>   format = "logfmt"
> }
> discovery.kubernetes "pods" {
>   role = "pod"
> }
> discovery.kubernetes "nodes" {
>   role = "node"
> }
> discovery.kubernetes "services" {
>   role = "service"
> }
> discovery.kubernetes "endpoints" {
>   role = "endpoints"
> }
> discovery.kubernetes "endpointslices" {
>   role = "endpointslice"
> }
> discovery.kubernetes "ingresses" {
>   role = "ingress"
> }
> loki.write "logs" {
>   endpoint {
>     url = "http://4.178.138.23:3100/loki/api/v1/push"
>   }
> }
> discovery.kubernetes "pod" {
>   role = "pod"
> }
> local.file_match "node_logs" {
>   path_targets = [{
>       __path__  = "/var/log/syslog",
>       job       = "node/syslog",
>       node_name = sys.env("HOSTNAME"),
>       cluster   = "ExertisFR",
>   }]
> }
> loki.source.file "node_logs" {
>   targets    = local.file_match.node_logs.targets
>   forward_to = [loki.write.logs.receiver]
> }
> discovery.relabel "pod_logs" {
>   targets = discovery.kubernetes.pod.targets
>   rule {
>     source_labels = ["__meta_kubernetes_namespace"]
>     action = "replace"
>     target_label = "namespace"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_pod_name"]
>     action = "replace"
>     target_label = "pod"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_pod_container_name"]
>     action = "replace"
>     target_label = "container"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_pod_label_app_kubernetes_io_name"]
>     action = "replace"
>     target_label = "app"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_namespace", "__meta_kubernetes_pod_container_name"]
>     action = "replace"
>     target_label = "job"
>     separator = "/"
>     replacement = "$1"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_pod_uid", "__meta_kubernetes_pod_container_name"]
>     action = "replace"
>     target_label = "__path__"
>     separator = "/"
>     replacement = "/var/log/pods/*$1/*.log"
>   }
>   rule {
>     source_labels = ["__meta_kubernetes_pod_container_id"]
>     action = "replace"
>     target_label = "container_runtime"
>     regex = "^(\\S+):\\/\\/.+$"
>     replacement = "$1"
>   }
> }
> loki.source.kubernetes "pod_logs" {
>   targets    = discovery.relabel.pod_logs.output
>   forward_to = [loki.process.pod_logs.receiver]
> }
> loki.process "pod_logs" {
>   stage.static_labels {
>       values = {
>         cluster = "ExertisFR",
>       }
>   }
>   forward_to = [loki.write.logs.receiver]
> }
> loki.source.kubernetes_events "cluster_events" {
>   job_name   = "integrations/kubernetes/eventhandler"
>   log_format = "logfmt"
>   forward_to = [
>     loki.process.cluster_events.receiver,
>   ]
> }
> loki.process "cluster_events" {
>   forward_to = [loki.write.logs.receiver]
>   stage.static_labels {
>     values = {
>       cluster = "ExertisFR",
>     }
>   }
>   stage.labels {
>     values = {
>       kubernetes_cluster_events = "job",
>     }
>   }
> }


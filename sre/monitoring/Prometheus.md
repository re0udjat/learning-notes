# Prometheus

## Prometheus Architecture

The following diagram illustrates the architecture of Prometheus:

![Prometheus Architecture](../../images/sre/monitoring/prometheus/prometheus-architecture.png)

- **Node Exporter:** Component which be installed to all Nodes in cluster by DaemonSet. It exposes a wide variety of harware- and kernel-related metrics.

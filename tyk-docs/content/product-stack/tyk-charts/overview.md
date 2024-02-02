---
title: Overview
description: Explains an overview of Tyk charts
tags: ["Tyk charts", "helm charts", "helm", "charts", "kubernetes", "k8s"]
---

## Tyk Charts

Tyk is working to provide a new set of Helm charts, and will progressively roll them out at [tyk-charts](https://github.com/TykTechnologies/tyk-charts). It will provide component charts for all Tyk Components, as well as umbrella charts as reference configurations for open source and Tyk Self Managed users.

### Status of the New Charts

| Umbrella Charts | Description | Status |
|-----------------|-------------|--------|
| tyk-oss            | Tyk Open Source                       | Stable              |
| tyk-stack          | Tyk Self Managed (Single Data Center) | Stable              |
| tyk-control-plane  | Tyk Self Managed (Distributed) Control Plane | Coming Soon     |
| tyk-data-plane     | Tyk Self Managed (Distributed) Data Plane <br> Tyk Hybrid Data Plane | Stable              |


To deploy Tyk OSS using the new Helm chart, please use [tyk-oss]{{<ref "/product-stack/tyk-charts/tyk-oss-chart">}}

To deploy hybrid data planes using the new Helm chart, please use [tyk-data-plane]{{<ref "/product-stack/tyk-charts/tyk-data-plane-chart">}}

To deploy Tyk Self Managed (for single data center) using the new helm chart, please use [tyk-stack]{{<ref "/product-stack/tyk-charts/tyk-stack-chart">}}
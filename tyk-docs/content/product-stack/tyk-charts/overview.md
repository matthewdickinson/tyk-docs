---
title: Overview
description: Explains an overview of Tyk charts
tags: ["Tyk charts", "helm charts", "helm", "charts", "kubernetes", "k8s"]
---

Tyk is working to provide a new set of helm charts, and will progressively roll them out at [tyk-charts](https://github.com/TykTechnologies/tyk-charts). It will provide component charts for all Tyk Components, as well as umbrella charts as reference configurations for open source and Tyk Self Managed users. Please check out the latest status from our [Github repository](https://github.com/TykTechnologies/tyk-charts).


## Which chart is right for you?

| Umbrella Charts | Descriptions |
|-----------------|-------------|
| [tyk-oss]({{<ref "/product-stack/tyk-charts/tyk-oss-chart">}})                      | Deploys Open source Tyk Gateway and Pump |
| [tyk-stack]({{<ref "/product-stack/tyk-charts/tyk-stack-chart">}})                  | Deploys Tyk stack including Tyk Dashboard, Gateway, Enterprise Portal, and Pump |
| [tyk-control-plane]({{<ref "/product-stack/tyk-charts/tyk-control-plane-chart">}}) <br> (Currently in Beta) | Deploys Tyk control plane including Tyk Dashboard, MDCB, Management Gateway, and Enterprise Portal. Tyk control plane manages and configures distributed data planes in separate clusters / regions. |
| [tyk-data-plane]({{<ref "/product-stack/tyk-charts/tyk-data-plane-chart">}})        | Deploys Tyk data plane including Tyk Gateway and Pump. Data planes are managed by Tyk Dashboard and MDCB in control plane. |

Learn more about [Tyk Licensing and Deployment models]({{<ref "/tyk-on-premises/licensing">}}).
---
title: Istio
---

_从v2.3.0版本开始支持_

[Istio](https://istio.io/)是一种开源工具，可使DevOps团队更轻松地观察，控制，故障排除和保护复杂的微服务网络中的流量。

随着微服务网络的变化和增长，它们之间的交互会变得更加难以管理和理解。在这种情况下，将服务网格作为单独的基础结构层很有用。Istio的服务网格使您可以操纵微服务之间的流量，而无需直接更改微服务。

我们与Istio的集成旨在使Rancher运维（例如管理员或集群所有者）可以将Istio交付给开发人员。然后，开发人员可以使用Istio实施安全策略，解决问题或管理绿色/蓝色部署，金丝雀部署或A / B测试的流量。

服务网格提供的功能包括但不限于以下功能：

- 流量管理功能
- 增强的监视和跟踪
- 服务发现和路由
- 使用双向TLS的安全连接和服务到服务的身份验证
- 负载均衡
- 自动重试，退避和断路

在集群中启用Istio之后，您可以通过 `kubectl` 来使用Istio的控制平面功能。

Rancher的Istio集成附带了全面的可视化辅助工具：

- **使用Jaeger跟踪错误的根本原因。** [Jaeger](https://www.jaegertracing.io/)是一个开放源代码工具，它为分布式跟踪系统提供UI，这对于根本原因分析和确定导致性能下降的原因很有用。分布式跟踪使您可以查看整个调用链，这些调用可能源于用户请求并遍历数十个微服务。
- **使用Kiali全面了解您的微服务架构。** [Kiali](https://www.kiali.io/)提供了一个图表，显示了服务网格中的服务及其连接方式，包括流量速率和它们之间的延迟。您可以检查服务网格的运行状况，或深入查看单个组件的传入和传出请求。
- **使用Grafana仪表板从时间序列分析中获得见解。** [Grafana](https://grafana.com/)是一个分析平台，可让您查询，可视化，警告和了解Prometheus收集的数据。
- **使用Prometheus UI编写时间序列数据的自定义查询。** [Prometheus](https://prometheus.io/)是系统监视和警报工具包。Prometheus会从您的集群中抓取数据，然后由Grafana使用。Prometheus UI也集成到Rancher中，使您可以编写时间序列数据的自定义查询并在UI中查看结果。

## 先决条件

在启用Istio之前，我们建议您确认Rancher worker节点具有足够的[CPU和内存](/docs/cluster-admin/tools/istio/resources)以运行Istio的所有组件。

## 设定指南

有关如何设置Istio并将其在项目中使用的说明，请参考[设定指南](/docs/cluster-admin/tools/istio/setup)。

## 禁用Istio

要从集群，命名空间或工作负载中删除Istio组件，请参阅[禁用Istio](/docs/cluster-admin/tools/istio/disabling-istio)。

## 访问可视化

> 默认情况下，只有集群所有者才能访问Jaeger和Kiali。关于如何允许项目成员访问它们的说明，请参阅[访问可视化](/docs/cluster-admin/tools/istio/rbac/#access-to-visualizations)。

在集群中设置Istio之后，Rancher UI中将提供Grafana，Prometheus，Jaeger和Kiali访问。

您对可视化的访问取决于您的角色。Grafana和Prometheus仅适用于集群所有者。默认情况下，Kiali和Jaeger UI仅对集群所有者可用，但是集群所有者可以通过编辑Istio设置来允许项目成员访问它们。当您转到项目并单击**资源> Istio**时，您可以通过单击页面右上角的图标来转到Kiali，Jaeger，Grafana和Prometheus各自的UI。

要查看可视化效果，请转到设置Istio的集群，然后单击**工具 > Istio**。您能在页面顶部看到每个UI的链接。

您还可以从项目视图访问可视化工具。

## 查看Kiali流量图

1. 在Rancher的项目视图中，单击**资源 > Istio**。
1. 如果您是集群所有者，则可以转到**流量图**标签页。该标签页将Kiali网络可视化集成到UI中。

## 查看流量指标

Istio的监控功能使您可以查看所有服务的性能。

1. 在Rancher中的项目视图中，单击**资源 > Istio**。
1. 转到**流量指标**标签。在集群中生成流量之后，您应该能够看到**成功率，请求量，4xx响应计数，项目5xx响应计数**和**请求持续时间**的指标。集群所有者可以查看所有指标 ，而项目成员可以查看指标的一部分。

## 架构

Istio安装了一个服务网格，该网格使用[Envoy](https://www.envoyproxy.io/learn/service-mesh)sidecar代理来拦截到每个工作负载的流量。这些sidecar拦截并管理服务到服务的通信，从而可以对集群内的流量进行细粒度的观察和控制。

Istio只能跟踪和控制已注入Istio sidecar的工作负载。

在Rancher中启用Istio将启用集群监控，并在集群中创建的所有新命名空间中启用Istio。您需要在预先存在的命名空间中手动启用Istio。

当命名空间启用Istio时，在命名空间中部署的新工作负载将自动具有Istio sidecar。您需要在预先存在的工作负载中手动启用Istio。

有关Istio sidecar的更多信息，请参阅[Istio文档](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)。

#### 两个入口

默认情况下，每个Rancher配置的集群都有一个NGINX ingress控制器，允许流量进入集群。要允许Istio接收外部流量，您需要为集群启用Istio ingress网关。结果是您的集群将有两个入口。

![在启用Istio的集群中，您可以有两个入口：默认的Nginx ingress控制器和默认的Istio控制器。](/img/rancher/istio-ingress.svg)

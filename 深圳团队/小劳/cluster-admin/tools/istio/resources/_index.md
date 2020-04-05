---
title: CPU和内存分配
---
_从v2.3.0版本开始支持_

本节介绍了集群中Istio组件的最低推荐计算资源。

每个组件的CPU和内存分配都是 [可配置的](#configuring-resource-allocations)。

启用Istio之前，建议您确认Rancher worker节点具有足够的CPU和内存来运行Istio的所有组件。

> **提示：** 在较大型的部署中，强烈建议通过为每个Istio组件添加节点选择器，将基础结构放置在集群中的专用节点上。

下表汇总了建议的最低资源要求以及每个Istio核心组件的CPU和内存限制。

在Kubernetes中，资源请求意味着除非该节点至少具有指定数量的可用内存和CPU，否则不会在该节点上部署工作负载。如果工作负载超过CPU或内存的限制，则可以终止该工作负载或将其从节点中逐出。有关管理容器资源限制的更多信息，请参考 [Kubernetes文档](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/)。

工作负载 | 容器 | CPU - 请求 | Mem - 请求 | CPU - 限制 | Mem - 限制 | 是否可配置
---------|-----------|---------------|---------------|-------------|-------------|-------------
istio-pilot |discovery| 500m | 2048Mi | 1000m | 4096Mi | 是
 istio-telemetry |mixer| 1000m         | 1024Mi        | 4800m       | 4096Mi      | 是            
 istio-policy | mixer      | 1000m         | 1024Mi        | 4800m       | 4096Mi      | 是            
 istio-tracing   | jaeger     | 100m          | 100Mi         | 500m        | 1024Mi      | 是            
 prometheus      | prometheus | 750m          | 750Mi         | 1000m       | 1024Mi      | 是            
 grafana         | grafana    | 100m          | 100Mi         | 200m        | 512Mi       | 是            
 Others          | -        | 500m          | 500Mi         | -         | -         | 否            
 **Total**           | **-**        | **3950m**         | **5546Mi**        | **>12300m**         | **>14848Mi**         | **-**   


## 配置资源分配

您可以为每种Istio组件类型分别配置资源分配。本节包括每个组件的默认资源分配。

为了更轻松地将工作负载调度到节点，集群管理员可以减少对该组件的CPU和内存资源请求。但是，默认的CPU和内存分配是我们建议的最小值。

您可以在[Istio官方文档](https://istio.io/docs/concepts/what-is-istio)中找到有关Istio配置的更多信息。

要配置分配给Istio组件的资源，

1. 在Rancher中，转到已安装Istio的集群。
1. 单击 **工具 > Istio**。这将打开Istio配置页面。
1. 更改CPU或内存分配，每个组件要被调度到的节点，或节点容忍。
1. 单击 **保存**。

**结果：** Istio组件的资源分配已更新。

### Pilot

[Pilot](https://istio.io/docs/ops/deployment/architecture/#pilot)组件提供以下功能：

- 认证配置
- Envoy sidecar的服务发现
- 用于智能路由的流量管理功能（A/B测试和金丝雀发布）
- 弹性配置（超时，重试，断路器等）

有关Pilot组件的更多信息，请参阅[文档](https://istio.io/docs/concepts/traffic-management/#pilot-and-envoy).

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
Pilot CPU限制 | Istio-pilot pod的CPU资源限制。 | 是      | 1000
Pilot CPU预留 | Istio-pilot pod的CPU资源预留。 | 是      | 500
Pilot Memory限制 | Istio-pilot pod的内存资源限制。 | 是      | 4096
Pilot Memory预留 | Istio-pilot pod的内存资源预留。 | 是      | 2048
跟踪抽样比例 | [跟踪抽样的比例](https://istio.io/docs/tasks/telemetry/distributed-tracing/overview/#trace-sampling) | 是      | 1
Pilot 结点选择器 | 能够选择将istio-pilot pod部署到的节点。要使用此选项，节点必须带有对应标签。 | 否       | n/a

### Mixer

[Mixer](https://istio.io/docs/ops/deployment/architecture/#mixer)组件跨服务网格实施访问控制和使用策略。它还与用于监视工具（例如Prometheus）的插件集成。Envoy sidecar将遥测数据和监视数据传递给Mixer，而Mixer将监视数据传递给Prometheus。

有关Mixer，策略和遥测的更多信息，请参阅[文档](https://istio.io/docs/concepts/policies-and-telemetry/)。

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
Mixer Telemetry CPU限制 | Istio-telemetry pod的CPU资源限制。 | 是                      | 4800
Mixer Telemetry CPU预留 | Istio-telemetry pod的CPU资源预留。 | 是                      | 1000
Mixer Telemetry Memory限制 | Istio-telemetry pod的内存资源限制。 | 是                      | 4096
Mixer Telemetry Memory预留 | Istio-telemetry pod的内存资源预留。 | 是                      | 1024
Enable Mixer Policy | 是否部署istio-policy。 | 是                      | False
Mixer Policy CPU限制 | Istio-policy pod的CPU资源限制。 | 是，当Policy启用时 | 4800
Mixer Policy CPU预留 | Istio-policy pod的CPU资源预留。 | 是，当Policy启用时 | 1000
Mixer Policy Memory限制 | Istio-policy pod的内存资源限制。 | 是，当Policy启用时 | 4096
Mixer Policy Memory预留 | Istio-policy pod的内存资源预留。 | 是，当Policy启用时 | 1024
Mixer 结点选择器 | 能够选择将istio-policy和istio-telemetry pods部署到的节点。要使用此选项，节点必须带有对应标签。 | 否                       | n/a

### Tracing

[分布式跟踪](https://istio.io/docs/tasks/telemetry/distributed-tracing/overview/)使用户可以通过服务网格跟踪请求。这使解决延迟，并行性和序列化问题变得更加容易。

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
启用跟踪 | 是否部署istio-tracing。 | 是 | 是
Tracing CPU限制 | Istio-tracing pod的CPU资源限制。  | 是      | 500
Tracing CPU预留 | Istio-tracing pod的CPU资源预留。 | 是      | 100
Tracing Memory限制 | Istio-tracing pod的内存资源限制。 | 是      | 1024
Tracing Memory预留 | Istio-tracing pod的内存资源预留。 | 是      | 100
Tracing 结点选择器 | 能够选择将tracing pod部署到的节点。要使用此选项，节点必须带有对应标签。 | 否       | n/a

### Ingress网关

Istio网关允许将Istio功能（例如监视和路由规则）应用于进入集群的流量。此网关是外部流量向Istio发出请求的先决条件。

有关更多信息，请参阅[文档](https://istio.io/docs/tasks/traffic-management/ingress/).

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
启用Ingress网关 | 是否部署istio-ingressgateway。 | 是      | 否
Ingress网关的服务类型 | 暴露网关服务的方式。你可以选择NodePort或Loadbalancer | 是      | NodePort
Http2端口 | http2请求的NodePort端口  | 是      | 31380
Https端口 | https请求的NodePort端口  | 是      | 31390
Load Balancer IP | Ingress网关的负载均衡器IP | 否       | n/a
负载均衡器IP源范围 | Ingress网关负载均衡器IP源的范围 | 否       | n/a
Ingress Gateway CPU限制 | Istio-ingressgateway的CPU资源限制。 | 是      | 2000
Ingress Gateway CPU预留 | Istio-ingressgateway的CPU资源预留。 | 是      | 100
Ingress Gateway Memory限制 | Istio-ingressgateway的内存资源限制。 | 是      | 1024
Ingress Gateway Memory预留 | Istio-ingressgateway的内存资源预留。 | 是      | 128
Ingress Gateway结点选择器 | 能够选择将Istio-ingressgateway pod部署到的节点。要使用此选项，节点必须带有对应标签。 | 否       | n/a

### Prometheus

您可以使用Prometheus查询Istio指标。Prometheus是一个开源系统监视和警报工具包。

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
Prometheus CPU限制 | Prometheus pod的CPU资源限制。 | 是      | 1000
Prometheus CPU预留 | Prometheus pod的CPU资源预留。 | 是      | 750
Prometheus Memory限制 | Prometheus pod的内存资源限制。 | 是      | 1024
Prometheus Memory预留 | Prometheus pod的内存资源预留。 | 是      | 750
Prometheus数据保留 | Prometheus实例保留数据的时长 | 是      | 6
Prometheus结点选择器 | 能够选择将Prometheus pod部署到的节点。要使用此选项，节点必须带有对应标签。 | 否       | n/a

### Grafana

您可以使用Grafana可视化指标。Grafana可让您可视化Prometheus抓取的Istio流量数据。

选项 | 描述| 是否必填项 | 默认值
-------|------------|-------|-------
启用Grafana | 是否部署Grafana。 | 是                                                         | 是
Grafana CPU限制 | Grafana pod的CPU资源限制。 | 是，当Grafana启用时                                   | 200
Grafana CPU预留 | Grafana pod的CPU资源预留。 | 是，当Grafana启用时                                   | 100
Grafana Memory限制 | Grafana pod的内存资源限制。 | 是，当Grafana启用时                                   | 512
Grafana Memory预留 | Grafana pod的内存资源预留。 | 是，当Grafana启用时                                   | 100
Grafana结点选择器 | 能够选择将Grafana pod部署到的节点。要使用此选项，节点必须带有对应标签。 | 否                                                          | n/a
启用Grafana持久存储 | 启用Grafana持久存储 | 是，当Grafana启用时                                   | 否
存储源 | 使用存储类来配置新的持久卷或使用现有的持久卷声明 | 是，当Grafana及其持久存储启用时                    | 使用存储类
存储类 | Grafana存储使用的存储类型 | 是，当Grafana及其持久存储启用，且存储源为存储类时 | 使用默认存储类型
持久卷大小 | Grafana所用的持久卷大小 | 是，当Grafana及其持久存储启用，且存储源为存储类时 | 5Gi
现有的持久卷声明 | Grafana使用的现有的持久卷声明 | 是，当Grafana及其持久存储启用，且存储源为现有的持久卷声明时  | n/a

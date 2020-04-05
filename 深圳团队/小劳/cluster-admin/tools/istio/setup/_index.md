---
title: 设定指南
---

本节介绍如何启用Istio并开始在项目中使用它。

本节假定您已安装Rancher，并且您有一个要设置Istio的Rancher创建的Kubernetes集群。

如果使用Istio进行流量管理，则需要允许外部流量进入集群。在这种情况下，您将需要执行以下所有步骤。

> **快速设置** 如果不需要外部流量到达Istio，而只想设置Istio来监控和跟踪集群中的流量，请跳过[设置Istio网关](/docs/cluster-admin/tools/istio/setup/gateway)和[设置Istio流量管理组件](/docs/cluster-admin/tools/istio/setup/set-up-traffic-management)的步骤。

1. [在集群中启用Istio](/docs/cluster-admin/tools/istio/setup/enable-istio-in-cluster)。
1. [在所有要使用Istio的命名空间中启用它](/docs/cluster-admin/tools/istio/setup/enable-istio-in-namespace)。
1. [选择部署主要Istio组件的节点](/docs/cluster-admin/tools/istio/setup/node-selectors)。
1. [添加有Istio sidecar注入的部署和服务](/docs/cluster-admin/tools/istio/setup/deploy-workloads)。
1. [设置Istio网关](/docs/cluster-admin/tools/istio/setup/gateway)。
1. [设置Istio的流量管理组件](/docs/cluster-admin/tools/istio/setup/set-up-traffic-management)。
1. [产生流量并在实际操作中查看Istio](#generate-traffic-and-see-istio-in-action)。

## 先决条件

本指南假定您已经[安装Rancher](/docs/installation)并且已经[创建一个单独的Kubernetes集群](/docs/cluster-provisioning)。指南步骤将在该集群安装Istio。

集群中的节点必须满足[CPU和内存要求](/docs/cluster-admin/tools/istio/resources/)。

您希望由Istio控制的工作负载和服务必须满足[Istio的要求](https://istio.io/docs/setup/additional-setup/requirements/)。

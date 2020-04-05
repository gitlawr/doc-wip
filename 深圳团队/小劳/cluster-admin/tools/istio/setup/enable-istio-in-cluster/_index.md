---
title: 1. 在集群中启用Istio
---

该集群使用默认的Nginx控制器来允许流量进入集群。

Rancher的[管理员](/docs/admin-settings/rbac/global-permissions/)或者[集群所有者](/docs/admin-settings/rbac/cluster-project-roles/#cluster-roles)可以在一个Kubernetes集群中部署Istio。

1. 从**全局**视图中，导航到要启用Istio的集群。
1. 单击**工具 > Istio**。
1. 可选: 为Istio组件配置成员访问权限和[资源限制](/docs/cluster-admin/tools/istio/resources/)。确保您的worker节点上有足够的资源来启用Istio。
1. 单击**启用**。
1. 单击**保存**。

**结果：** 在集群层级启用了Istio。

Istio会作为一个名为`cluster-istio`的[应用](/docs/catalog/apps/)添加到该集群的`系统`项目中。

在集群中启用Istio时，Istio sidecar自动注入的标签`istio-injection = enabled`将自动添加到该集群中的每个新的命名空间。这会自动在这些命名空间中部署的所有新工作负载中启用Istio sidecar注入。您将需要在预先存在的命名空间和工作负载中手动启用Istio。

#### [下一步：在命名空间中启用Istio](/docs/cluster-admin/tools/istio/setup/enable-istio-in-namespace)

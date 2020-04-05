---
title: 5. 设置Istio网关
---

每个集群的网关可以拥有自己的端口或负载均衡器，与服务网格无关。默认情况下，每个Rancher配置的集群都有一个NGINX Ingress控制器，允许流量进入集群。

您可以在安装或不安装Istio的情况下使用NGINX Ingress控制器。如果这是集群的唯一网关，则Istio将能够在服务之间路由通信，但是Istio将无法从集群外部接收通信。

要允许Istio接收外部流量，您需要启用Istio的网关，该网关充当外部流量的南北代理。当您启用Istio网关时，结果是您的集群将有两个入口。

您还需要为您的服务设置一个Kubernetes网关。这一Kubernetes资源指向Istio对集群的入口网关的实现。

您可以使用负载平衡器或者Istio的NodePort网关将流量路由到服务网格中。本节介绍如何设置NodePort网关。

有关Istio网关的更多信息，请参阅[Istio文档](https://istio.io/docs/reference/config/networking/v1alpha3/gateway/)。

![在启用Istio的集群中，您可以有两个入口：默认的Nginx Ingress和默认的Istio控制器](/img/rancher/istio-ingress.svg)。

## 启用Istio网关

Ingress网关是将在您的集群中部署的Kubernetes服务。每个集群只有一个Istio网关。

1. 转到要允许外部流量进入Istio的集群。
1. 单击**工具 > Istio**。
1. 展开**Ingress网关**部分。
1. 在**启用Ingress网关**部分，单击**是**。Istio网关的默认服务类型是NodePort。您也可以将其配置为[负载均衡器](/docs/k8s-in-rancher/load-balancers-and-ingress/load-balancers/)。
1. （可选）配置该服务的端口，服务类型，节点选择器和容忍以及资源请求和限制。建议的最低资源是对CPU和内存的默认资源请求。
1. 单击**保存**。

**结果：** 部署了网关，这使Istio可以从集群外部接收流量。

## 添加一个指向Istio网关的Kubernetes网关

为了允许流量到达Ingress，您还需要在YAML中提供一个Kubernetes网关资源，该资源指向Istio对集群的Ingress网关的实现。

1. 转到要在其中部署Kubernetes网关的命名空间，然后单击**导入YAML**。
1. 将网关YAML作为文件上传或粘贴到表单中。下面提供了示例网关YAML。
1. 单击**导入**。

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
    - port:
        number: 80
        name: http
        protocol: HTTP
      hosts:
        - '*'
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
    - '*'
  gateways:
    - bookinfo-gateway
  http:
    - match:
        - uri:
            exact: /productpage
        - uri:
            prefix: /static
        - uri:
            exact: /login
        - uri:
            exact: /logout
        - uri:
            prefix: /api/v1/products
      route:
        - destination:
            host: productpage
            port:
              number: 9080
```

**结果：** 您已经配置了网关资源，以便Istio可以从集群外部接收流量。

通过运行以下命令确认资源是否存在：

```
kubectl get gateway -A
```

预期的结果是输出与下方类似的信息：

```
NAME               AGE
bookinfo-gateway   64m
```

#### 从Web浏览器访问ProductPage服务

要测试并查看BookInfo应用程序是否正确部署，可以使用Istio控制器IP和端口以及您的Kubernetes网关资源中指定的请求名称，在Web浏览器中查看该应用程序：

`http://<IP of Istio controller>:<Port of istio controller>/productpage`

要获取入口网关的URL和端口，

1. 转到集群中的`系统`项目。
1. 在`系统`项目中，转到`资源` > `工作负载`，然后向下滚动到`istio-system`命名空间。
1. 在`istio-system`内，有一个名为`istio-ingressgateway`的工作负载。在此工作负载的名称下，您应该看到像`80/tcp`这样的链接。
1. 单击这些链接之一。这将在您的Web浏览器中显示入口网关的URL。将`/productpage`附加到URL。

**结果：** 您应该在Web浏览器中看到BookInfo应用。

关于如何获取Istio控制器的URL和端口，请参阅[Istio文档](https://istio.io/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports)中的指令。

## 故障排除

[Istio官方文档](https://istio.io/docs/tasks/traffic-management/ingress/ingress-control/#troubleshooting)建议使用`kubectl`命令检查正确的入口主机和入口端口是否有外部请求。

#### 确认Kubernetes网关与Istio的Ingress控制器匹配

您可以尝试执行本节中的步骤，以确保正确配置Kubernetes网关。

在网关资源中，选择器通过其标签引用Istio的默认Ingress控制器，其中标签的键为`istio`，值为`ingressgateway`。为确保标签适用于网关，请执行以下操作：

1. 转到集群中的`系统`项目。
1. 在`系统`项目中，转到命名空间`istio-system`。
1. 在`istio-system`命名空间中，找到一个名为`istio-ingressgateway`的工作负载。
1. 单击此工作负载的名称，然后转到**标签/注释**部分。您应该看到它具有键`istio`和值`ingressgateway`。这确认网关资源中的选择器与Istio的默认Ingress控制器匹配。

#### [下一步：设置Istio的流量管理组件](/docs/cluster-admin/tools/istio/setup/set-up-traffic-management)

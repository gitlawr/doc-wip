---
title: 4. 添加带Istio sidecar的部署和服务
---

> **先决条件：** 要为工作负载启用Istio，集群和命名空间必须启用Istio。

在命名空间中启用Istio后仅对新的工作负载启用自动Sidecar注入。要为现有工作负载启用Envoy Sidecar，您需要为每个工作负载手动启用它。

要将Istio sidecar注入命名空间中的现有工作负载，请转到工作负载页面，单击**省略号 (...)**，然后单击**重新部署**。重新部署工作负载时，Envoy sidecar会自动注入。

等待几分钟，以使工作负载升级到带有Istio sidecar。单击该工作负载，然后转到**容器**部分。您应该能够在工作负载中看到istio-init和istio-proxy容器。这意味着已为工作负载启用了Istio sidecar。Istio正在为Envoy sidecar进行所有接线。现在，如果您通过Yaml中使用了Istio的功能，则Istio可以自动执行。

### 3. 添加部署和服务

接下来，我们再添加Istio文档中的BookInfo示例应用程序相关的Kubernetes资源。

1. 进入要部署工作负载的项目中。
1. 在工作负载页面，单击**导入YAML**。
1. 将以下资源复制到表单中。
1. 单击**导入**。

这将从Istio的BookInfo示例应用中设置以下资源：

Details部署和服务:

- 一个名为`details`的Service
- 一个名为`bookinfo-details`的ServiceAccount
- 一个名为`details-v1`的Deployment

Ratings部署和服务:

- 一个名为`ratings`的Service
- 一个名为`bookinfo-ratings`的ServiceAccount
- 一个名为`ratings-v1`的Deployment

Reviews部署和服务（三个版本）：

- 一个名为`reviews`的Service
- 一个名为`bookinfo-reviews`的ServiceAccount
- 一个名为`reviews-v1`的Deployment
- 一个名为`reviews-v2`的Deployment
- 一个名为`reviews-v3`的Deployment

Productpage部署和服务:

这是应用程序的主页，可从网页浏览器中看到。其他服务将从此页面调用。

- 一个名为`productpage`的Service
- 一个名为`bookinfo-productpage`的ServiceAccount
- 一个名为`productpage-v1`的Deployment

### 资源YAML

```yaml
# Copyright 2017 Istio Authors
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

##################################################################################################
# Details service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: details
  labels:
    app: details
    service: details
spec:
  ports:
    - port: 9080
      name: http
  selector:
    app: details
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookinfo-details
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: details-v1
  labels:
    app: details
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: details
      version: v1
  template:
    metadata:
      labels:
        app: details
        version: v1
    spec:
      serviceAccountName: bookinfo-details
      containers:
        - name: details
          image: docker.io/istio/examples-bookinfo-details-v1:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---
##################################################################################################
# Ratings service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: ratings
  labels:
    app: ratings
    service: ratings
spec:
  ports:
    - port: 9080
      name: http
  selector:
    app: ratings
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookinfo-ratings
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ratings-v1
  labels:
    app: ratings
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ratings
      version: v1
  template:
    metadata:
      labels:
        app: ratings
        version: v1
    spec:
      serviceAccountName: bookinfo-ratings
      containers:
        - name: ratings
          image: docker.io/istio/examples-bookinfo-ratings-v1:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---
##################################################################################################
# Reviews service
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: reviews
  labels:
    app: reviews
    service: reviews
spec:
  ports:
    - port: 9080
      name: http
  selector:
    app: reviews
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookinfo-reviews
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reviews-v1
  labels:
    app: reviews
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: reviews
      version: v1
  template:
    metadata:
      labels:
        app: reviews
        version: v1
    spec:
      serviceAccountName: bookinfo-reviews
      containers:
        - name: reviews
          image: docker.io/istio/examples-bookinfo-reviews-v1:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reviews-v2
  labels:
    app: reviews
    version: v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: reviews
      version: v2
  template:
    metadata:
      labels:
        app: reviews
        version: v2
    spec:
      serviceAccountName: bookinfo-reviews
      containers:
        - name: reviews
          image: docker.io/istio/examples-bookinfo-reviews-v2:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reviews-v3
  labels:
    app: reviews
    version: v3
spec:
  replicas: 1
  selector:
    matchLabels:
      app: reviews
      version: v3
  template:
    metadata:
      labels:
        app: reviews
        version: v3
    spec:
      serviceAccountName: bookinfo-reviews
      containers:
        - name: reviews
          image: docker.io/istio/examples-bookinfo-reviews-v3:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---
##################################################################################################
# Productpage services
##################################################################################################
apiVersion: v1
kind: Service
metadata:
  name: productpage
  labels:
    app: productpage
    service: productpage
spec:
  ports:
    - port: 9080
      name: http
  selector:
    app: productpage
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookinfo-productpage
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: productpage-v1
  labels:
    app: productpage
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: productpage
      version: v1
  template:
    metadata:
      labels:
        app: productpage
        version: v1
    spec:
      serviceAccountName: bookinfo-productpage
      containers:
        - name: productpage
          image: docker.io/istio/examples-bookinfo-productpage-v1:1.15.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 9080
---

```

### [下一步：设置Istio网关](/docs/cluster-admin/tools/istio/setup/gateway)

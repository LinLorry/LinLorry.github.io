## Kubernetes 介绍和简单使用

### 介绍

Kubernetes（K8s）按照官方的介绍是

> an open-source system for automating deployment, scaling, and management of containerized applications

即一个拥有自动部署、云和管理容器的一个开源应用。

它拥有很多功能，均衡负载、集群管理、容器健康检测、自动停止或回滚容器、安全配置管理等等。

### 学习使用之前

在学习如何使用K8s之前，得先明白K8s的几个概念:
- [Node](#node)
- [Deployment](#deployment)
- [Service](#service)
- [Pod](#pod)


### Node

Node，节点

> A node is a worker machine in Kubernetes,previously known as a `minion`. A node may be a VM or physical machine, depending on the cluster.

节点是K8s中组成集群的单位，它可以是一个物理主机，也可以是一个虚拟主机。在每个节点上会有容器运行服务、kubelet服务和kube-proxy服务。

每个节点都有许多状态和信息，并且因为节点不是由K8s自身创建启动的，是由用户自己启动加入的，所以用户对其的配置和控制比较重要，具体见[Node官方文档](https://kubernetes.io/docs/concepts/architecture/nodes/)

### Deployment

Deployment，部署对于K8s来说是一个任务，在完成一个部署的配置后，提交给K8s，K8s的Deployment controller会受理该部署，执行相应的部署操作，更新或创建一个部署，按照要求更新或创建对应数量的Pods。

它是本文的重点，在后文会详细介绍。

### Service

Service，服务

> An abstract way to expose an application running on a set of Pods as a network service.

简单来说，因为K8s为了控制外部访问内部服务，对容器的网络进行控制，为它们单独分配IP地址，并且修改路由，使外部无法直接访问集群内部服务，而通过Service，对外部请求进行管理，针对部署开放端口，使外部访问对应端口才能访问对应服务。

### Pod

> Pods are the smallest deployable units of computing that can be created and managed in Kubernetes.

Pod是能过被K8s创建和管理的最小单元。

Pod由一个或一组容器组成，K8s会为一个Pod设置一个IP地址和若干个数据卷，属于该Pod的所有容器共享这个IP和这些数据卷，使该Pod内的所有的容器都具有相同网络、存储空间和上下文。

## 学习

了解了K8s的一些基本概念后，就可以开始学习K8s的简单使用了，但因为本文只是简单的入门，并不会深入涉及K8s，所以并不能做到面面俱到，有需要的可以访问[官方文档](https://kubernetes.io/docs/home/)。

这里将介绍如何创建一个部署（Deployment），并启动一个服务（Service），使外界可以访问内部运行的应用。

创建一个部署，大部分时候分为下面几步：
- [构建应用镜像](#构建应用镜像)
- [描述对象](#描述对象)
- [创建](#创建)

### 构建应用镜像

因为K8s最终使用的容器运行服务，例如Docker，而容器的使用是基于镜像（image）的，所以，需要将应用程序构建成对应容器运行服务的镜像。关于构建相关的内容可以查看对应文档，这里不进行介绍。

### 描述对象

当完成镜像的构建后，我们可以开始编写部署的配置文件。对于K8s可以管理的所有对象都可以使用K8s API进行管理。大部分时候，使用.yaml文件对K8s对象进行配置。

下面是一个Deployment示例：

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

一个描述内必须有下面几项：
- `apiVersion`：使用的K8s API版本号
- `kind`：描述的对象类型（Deployment、Service、Pod等）
- `metadata`：标识该对象数据（name，UID，namespace）

而`spec`则是关于这个对象的具体描述，对应不同的对象类型，有不同的内容的选项，[Kubernetes API Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/)是官方的API文档，可以在这里找需要的配置选项。

#### `DeployemntSpec`

这里重点描述下Deployment的Spec配置，在1.15版本关于Deployment的Spec的字段有8个：

字段                                | 描述
:---------------------------------- | :---
`minReadySeconds`<br>integer        | 一个Pod被创建成功后到准备好的最小时间，默认为0
`paused`<br>Boolean                 | 描述该部署是否停止
`progressDeadlineSeconds`<br>integer| 当部署失败后重复执行部署的时间，在这段时间内，K8s会重复尝试部署，直到时间结束，默认为600秒
`replicas`<br>integer               | 启动的Pod数量，默认为1
`revisionHistoryLimit`<br>integer   | 该部署可以被回滚的最大历史记录数，默认为10
`selector`<br>LabelSelector         | 该部署启动的Pod的标签，必须符合模板Pod的标签
`strategy`<br>DeploymentStrategy    | 替换已存在的Pod的策略
`template`<br>PodTemplateSpec       | 创建Pod的Spec模板

其他具体可参考[DeploymentSpec v1 官方文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#deploymentspec-v1-apps)

#### `PodTemplateSpec`

在`DeployemntSpec`的字段中有一个`template`，是`PodTemplateSpec`类型的，它是部署创建Pod的模板。PodTemplateSpec v1有两个字段：

字段                    | 描述
:---------------------- | :---
`metadata`<br>ObjectMeta| 标识该对象数据
`spec`<br>PodSpec       | 模板Pod的Spec字段

其他具体可参考[PodTemplateSpec v1 官方文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#podtemplatespec-v1-core)

#### `Container`

`PodSpec`是Pod的`spec`字段类型，其中的字段较多，这里只介绍其中的重点字段`containers`，它属于`Container array`类型，其他的字段详见[PodSpec v1 官方文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#podspec-v1-core)

以下Container的重点描述字段
字段                          | 描述
:---------------------------- | :---
`args`<br>string array        | 命令行参数
`command`<br>string array     | 容器启动执行命令
`env`<br>EnVar array          | 容器环境变量
`image`<br>string             | 容器镜像
`name`<br>string              | 容器名称
`ports`<br>ContainerPort array| 容器对外暴露端口列表

其他具体可参考[Container v1 官方文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#container-v1-core)

#### 例子：

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deplyment
  labels:
    app: app
spec:
  replicas: 4
  selector:
    matchLabels:
      app: app
    template:
      metadata:
        labels:
          app: app
      spec:
        containers:
        - name: app-fe
          image: app-fe:latest
          ports:
          - containerPort: 80
        - name: app-be
          image: app-be:lastest
          env:
          - name: CONFIG
            value: default
          - name: PASSWD
            value: password
          volumeMounts:
          - mountPath: /workdir/data
            name: app-data-volume
        volumes:
        - name: app-data-volume
          hostPath:
            path: /var/data/app
```

### 创建

当完成部署的配置后，就可以进行创建部署了。这里就要介绍到一个命令：`kubectl`

`kubectl`是管理K8s的命令，其命令格式是：

``` bash
kubectl [command] [TYPE] [NAME] [flags]
```

- command：`apply`, `create`, `describe`, `delete`, `get`等等
- TYPE: `deployment`, `pod`, `service`等等
- NAME：指定特定的对象，对象的唯一标识

在这里假设配置文件为`conf.yaml`，我们可以执行以下命令执行部署

``` bash
kubectl apply -f conf.yaml
```

如果配置文件没有格式错误或发生意外，将会有输出：

``` bash
deplyment/deplyment-name
```

为了查看部署情况，我们可以执行以下命令查看：

``` bash
kubectl get deployment deployment-name
```

输出：

``` bash
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
deployment-name   4/4     4            4          0s
```

后面的READY、UP-TO-DATE和AVAILABLE字段指代创建的Pod，如果想获取更多的信息，可执行以下命令：

``` bash
kubectl describe deployment deployment-name
```

它将会该部署的具体信息输出

如果希望获取Pod的信息，可以通过下面的命令获取：

``` bash
kubectl get pods
```

它会列出所有pod的名词、状态以及运行时间

自此，部署已经创建完成, 但对于这个应用来说，还没结束。正如之前的介绍，K8s对外网访问进行了控制，我们需要启动一个服务让外部可以访问应用。

### 启动服务

我们可以像部署一样，编写一个.yaml文件对服务进行描述([API文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#-strong-service-apis-strong-))

但我们也可以简单的执行一个命令为我们的部署创建一个服务。

``` bash
kubectl expose deployment deployment-name --type=NodePort
```

输出

``` bash
service/deployment-name exposed
```

然后执行下面的命令就可以查看开放的端口号了

``` bash
kubectl get service deployment-name
```

### 最后

最后，我们实现了一个应用的部署，并且开放了端口供外部访问。

K8s的内容有很多，这里只是简单的入门，希望这篇文章可以帮助到一些人。介于笔者的水平也不高，如果文内有什么错误，还请见谅。
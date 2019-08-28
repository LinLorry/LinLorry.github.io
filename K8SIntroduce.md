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

## 学习

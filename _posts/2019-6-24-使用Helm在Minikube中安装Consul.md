---
title: 使用Helm在Minikube中安装Consul
published: true
---

### [](#header-3)背景介绍

  Kubernetes管理容器集群，容器间的通信是很有必要的，通过硬编码的方式指定容器的IP地址不可移植且增加工作量，因此提供一个服务发现的服务是有必要的，Consul就是这样的一个工具。本文将讲述如何在Kubernetes中安装和使用Consul。

### [](#header-3)准备

  [**Helm**](https://helm.sh/)是Kubernetes的包管理工具，类似于Centos的yumd和Ubuntu的apt，用于包的发布和管理，功能十分强大。
  本文中简单地介绍如何使用helm安装Consul。

  [**Consul**](https://www.consul.io/)是用于服务发现和监测的服务，在我们前面的文章中，做了简单的介绍。Consul可以独立于Kubernetes使用，在一些简单的微服务架构中也可以用作服务发现的工具。

### [](#header-3)启动Minikube和安装Consul

```sh
  # 启动minikube, 可以指定运行内存
  minikube start --memory 4096

  # 启动控制UI界面 设置url参数
  minikube dashboard --url

  #为了完成测试可以先使用demo
  git clone https://github.com/hashicorp/demo-consul-101.git
  cd demo-consul-101/k8s

  # helm 安装
  snap install helm

  # helm初始化，在国内不能使用
  # helm init

  # 替代方案
  # 记下helm版本号，并修改下面init命令的对应的版本号
  helm version
  helm init --upgrade -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:版本号(例如V.2.14.1) --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

  # 修改配置conusl文件helm-consul-values.yaml如下：
  # Choose an optional name for the datacenter
    global:
      datacenter: minidc

    # Enable the Consul Web UI via a NodePort
    ui:
      service:
        type: 'NodePort'

    # Enable Connect for secure communication between nodes
    connectInject:
      enabled: true

    client:
      enabled: true
      grpc: true

    # Use only one Consul server for local development
    server:
      replicas: 1
      bootstrapExpect: 1
      disruptionBudget:
        enabled: true
        maxUnavailable: 0

  # 安装consul 通过指定文件，如果不使用name参数将会使用随机名字
  helm install -f helm-consul-values.yaml --name hedgehog ./consul-helm

  # 查看服务，可以通过如下命令也可以使用dashboard
  kubectl get pods
```

### [](#header-3)部署Consul可发现的应用

```sh
  # 通过ui查看consul，打开url
  minikube service hedgehog-consul-ui

  # 部署一个实例应用
  kubectl create -f 04-yaml-connect-envoy

  # 查看实例应用是否部署成功，打开 http://localhost:9002 
  kubectl port-forward dashboard 9002:9002
```

  这样表示部署完成，可以通过查看yaml文件查看配置，具体参见文件内容

### [](#header-3)使用Consul Connect

  在Consul UI界面里面可以通过Intentions栏创建已注册服务的网络连续连接情况，可以定义两个服务市质检是否可以访问，具体实际操作

---
title: Kubernetes初体验
published: true
---

> 本文主要介绍Kubernetes本地化配置


### [](#header-3)环境要求

1.  在 BIOS 中开启VT-x/AMD-v 虚拟化支持，这一项是必须的条件，但是目前大多数云主机都不支持这项，只能在本地机器上进行。如果主机不适合作为开发环境，可以在本地主机上使用vmvare软件创建一个适合的开发环境（在虚拟机设里面的虚拟换引擎勾选虚拟化Intel VT-x/RVI 选项重启即可）。

2.  minikube和kubectl安装，kubectl很好安装网上教程比较多略过，minikube官网上也有教程，但是**国内不能用**，因为使用了部分google的网址，所以会导致特别慢。针对此问题阿里云的开发者提供了国内可用的[minikube](https://yq.aliyun.com/articles/221687)，经过测试可以使用

3.  KVM安装，[ubuntu安装方式](https://help.ubuntu.com/community/KVM/Installation), 其他系统自行google。注意安装KVM之前需要按照教程检查主机是否支持虚拟化。


### [](#header-3)初体验

*   检查和启动

```sh
  sudo minikube version
  # 返回 minikube version: v1.1.1

  # 启动k8s
  sudo minikube start
  # 返回 🏄  Done! kubectl is now configured to use "minikube"
  # 表示环境没有问题 

  # 使用k8s客户端查看服务端和客户端版本号
  sudo kubectl version
  # 返回：
  # Client Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.3", GitCommit:"5e53fd6bc17c0dec8434817e69b04a25d8ae0ff0", GitTreeState:"clean", BuildDate:"2019-06-06T01:44:30Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
  # Server Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.3", GitCommit:"5e53fd6bc17c0dec8434817e69b04a25d8ae0ff0", GitTreeState:"clean", BuildDate:"2019-06-06T01:36:19Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}

```

*   获取集群和节点信息

```sh
  # 获取集群信息, 我们已经运行一个主节点和控制页，可以通过web去查看应用，
  sudo kubectl cluster-info

  # 获取节点信息，可以查看集群所有节点
  sudo kubectl get nodes
  # 返回，目前只有一个主节点运行
  # NAME       STATUS   ROLES    AGE   VERSION
  # minikube   Ready    master   3d    v1.14.3

```

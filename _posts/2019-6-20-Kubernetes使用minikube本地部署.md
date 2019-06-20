---
title: Kubernetes使用minikube本地化部署
published: true
---

**在上一篇文章《[Kubernetes初体验](https://wangdalei.github.io/Kubernetes%E5%88%9D%E4%BD%93%E9%AA%8C)》中我们介绍了本地化安装的一些限制条件，在本文中我们将基于上一篇配置好环境的基础之上，进行本地化部署**

### [](#header-3)前期检查

  可以使用下面命令检查本地化环境是否配置成功, 如果成功下面命令都会返回正常的结果

```sh
   # 查看服务器和客户端是否成功运行
   kubectl version

   # 查看集群信息
   kubectl cluster-info 

   # 查看节点信息
   kubectl get pods
```

### [](#header-3)部署官方推荐应用

  根据[官方文档](https://kubernetes.io/docs/setup/learning-environment/minikube/)的事例

```sh
  kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.10 --port=8080
```
  但是国内的环境是运行不成功的，可以通过kubectl get pods 和 kubectl get deployments查看镜像的状态。因为上述链接是google的，微软azure针对这个问题给过国内提供镜像，按照[这篇博文](https://ieevee.com/tech/2019/03/02/azure-gcr-proxy.html),把 'gcr.io/google-samples/' 替换成为 'gcr.azk8s.cn/google_containers/' 就可以成功拉取对应的镜像并执行，如下所示：
```sh
  # 从镜像地址拉取镜像，制定镜像内部使用端口为8080，此时镜像并没有真正执行。
  kubectl run hello-minikube --image=gcr.azk8s.cn/google_containers/echoserver:1.10 --port=8080

  # 查看部署已经就绪
  kubectl get deployments

  # 查看pod还没有真正执行
  kubectl get pods

  # 开启服务，让pod执行
  kubectl expose deployment hello-minikube --type=NodePort

  # 查看服务是否成功,此时可以看到与上述相比，状态已经发生变化，表示服务已经执行
  kubectl get pods

  # 通过url 访问服务， 此命令会返回一个url，在浏览器中打开，服务运行成功
  minikube service hello-minikube --url
```
  到此官方镜像部署成功

### [](#header-3)部署自己的镜像

  在阿里云开通 容器镜像服务 目前免费的，按照步骤推送自己的docker镜像到镜像服务之中

  其他步骤跟上述官方应用一致

  此外还有一个很重要的命令
```sh
  # 开启通过浏览器查看集群的信息
  minikube dashboard
```

### [](#header-3)其他高级特性
  持续更新

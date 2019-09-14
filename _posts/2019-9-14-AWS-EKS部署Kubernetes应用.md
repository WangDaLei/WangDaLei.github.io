---
title: AWS EKS部署Kubernetes应用
published: true
---

**_本文中使用eksctl来部署Kubernetes的应用_**

## [](#header-2)准备

*   安装并配置awscli

  ```
    # 使用aws信息登陆
    aws configure
    AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
    AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    Default region name [None]: us-west-2
    Default output format [None]: json
  ```

*   安装eksctl

  ```
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)\_amd64.tar.gz" | tar xz -C /tmp

    sudo mv /tmp/eksctl /usr/local/bin
    eksctl version
    # GitTag 版本应至少为 0.1.37
  ```

*   下载并安装 kubectl
 
  略

## [](#header-2)创建集群

  ```
    eksctl create cluster --name test-k8s --version 1.13 --nodegroup-name test-workers --node-type t3.medium --nodes 2 --nodes-min 1 --nodes-max 3 --node-ami auto
  ```

  执行完成之会在这个位置创建文件：/Users/****/.kube/config

  查看创建的集群，如下所示即创建成功

  ```
    kubectl get svc
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   1m
  ```

## [](#header-2)部署自己的应用

  以下内容都从[Kubernetes-Deployment](https://github.com/WangDaLei/Kubernetes-Deployment)库拉取代码，并部署该应用

  ```
    # 部署redis server 服务
    cd redis-server/
    kubectl apply -f redis-master-deployment.yaml
    kubectl apply -f redis-master-service.yaml

    # mysql server 服务
    cd ../mysql-server/
    kubectl apply -f gp2-storage-class.yaml
    # 修改mysql-server-pvc.yaml 中的ReadWriteMany为ReadWriteOnce
    kubectl apply -f gp2-storage-class.yaml
    kubectl apply -f mysql-server-pvc.yaml
    kubectl apply -f mysql-server-secret.yaml
    sudo kubectl apply -f mysql-server-deployment.yaml
    kubectl apply -f mysql-server-service.yaml

    # 部署app应用
    cd ../app/
    # 修改type: NodePort 为type: LoadBalancer
    kubectl apply -f app-deployment.yaml
    kubectl apply -f app-service.yaml

  ```

  等待一段时间后，就可以看到所有的pod和service都部署完成

  ```
    kubectl get pod
      ME                            READY   STATUS    RESTARTS   AGE
      app-69d7db5d6b-qd74z            1/1     Running   0          59m
      mysql-server-84f756bb5-d47db    1/1     Running   0          68m
      redis-master-6f855d55f8-rgqn8   1/1     Running   0          69m
    kubectl get service
      NAME           TYPE           CLUSTER-IP       EXTERNAL-IP                                                                   PORT(S)        AGE
      app            LoadBalancer   10.100.16.100    a56986b5ad69f11e98b10064a7947415-791164033.ap-southeast-1.elb.amazonaws.com   80:32670/TCP   59m
      kubernetes     ClusterIP      10.100.0.1       <none>                                                                        443/TCP        85m
      mysql-server   ClusterIP      10.100.116.200   <none>                                                                        3306/TCP       68m
      redis-master   ClusterIP      10.100.45.205    <none>                                                                        6379/TCP       69m
```

  验证是否部署完成：

    访问 http://a56986b5ad69f11e98b10064a7947415-791164033.ap-southeast-1.elb.amazonaws.com/api/test/system/

  结果如图所示
  ![](https://raw.githubusercontent.com/WangDaLei/WangDaLei.github.io/master/images/eks-respnse.jpg)

  表示部署成功。

---
title: 使用Kubernetes部署mysql
published: true
---

### [](#header-3)介绍：

Mysql是常用的数据库，本文介绍怎么在Kubernetes中部署和使用mysql。

Mysql与redis不同的是，MySQL默认用到磁盘存储数据，Mysql默认需要账号和密码。

下文中所有的资源来自[Kubernetes-Deployment](https://github.com/WangDaLei/Kubernetes-Deployment)

### [](#header-3)细节：

*   使用Secret存储密码信息, 如下所示，使用Secret存储两个变量，name和key，使用base64码表示

  ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    type: Opaque
    data:
      name: YWRtaW4=
      # admin
      key: MWYyZDFlMmU2N2Rm
      # 1f2d1e2e67df
  ```

*   使用PersistentVolumeClaim使用磁盘资源，可以指定访问模式，存储大小和存储类名

  ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pvc
      labels:
        app: mysql-pvc
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 2Gi
      storageClassName: standard
  ```

*   在mysql中使用Secret和PersistentVolumeClaim资源，环境变量MYSQL_ROOT_PASSWORD使用secret中的内容，容器内的/var/lib/mysql路径挂载在PersistentVolumeClaim申请的资源上。

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: mysql-server
    labels:
      app: python
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: python
        tier: mysql
    strategy:
        type: Recreate
    template:
      metadata:
        labels:
          app: python
          tier: mysql
      spec:
        containers:
          - image: mysql
            name: mysql
            env:
              - name: MYSQL_ROOT_PASSWORD
                valueFrom:
                  secretKeyRef:
                    name: mysecret
                    key: key
            ports:
              - containerPort: 3306
                name: mysql
            volumeMounts:
              - name: mysql-persistent-storage
                mountPath: /var/lib/mysql
        volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pvc
  ```

*   app yaml配置，指定环境变量来自于mysecret

  ```
   ...
    - name: MYSQL_ROOT_PASSWORD
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: key
   ...
  ```

### [](#header-3)部署：

```
kubectl create -f mysql-server-secret.yaml
kubectl create -f mysql-server-pvc.yaml

kubectl apply -f mysql-server-deployment.yaml
kubectl apply -f mysql-server-service.yaml
```

这样就可以把MySQL部署好，下面部署app

```
cd ../redis-server/
kubectl apply -f redis-master-deployment.yaml
kubectl apply -f redis-master-service.yaml

cd ../app
kubectl apply -f app-deployment.yaml
kubectl apply -f app-service.yaml
```

这样就把app部署好了，可能需要一定时间下载和部署镜像

```
kubectl get pods

  NAME                            READY   STATUS    RESTARTS   AGE
  app-698fdccc9f-j7bs5            1/1     Running   3          4d23h
  mysql-server-69f77698bb-6q6jg   1/1     Running   1          4d23h
  redis-master-6c7fcffddf-qsxch   1/1     Running   1          4d23h

# 进入pod内部
kubectl exec -it app-698fdccc9f-j7bs5 bash

apt install iputils-ping
ping mysql-server
  PING mysql-server.default.svc.cluster.local (10.101.116.243) 56(84) bytes of data.

echo $MYSQL_ROOT_PASSWORD
  1f2d1e2e67df

# 输入删除密码
mysql -h mysql-server -uroot -p

# 登陆成功
```

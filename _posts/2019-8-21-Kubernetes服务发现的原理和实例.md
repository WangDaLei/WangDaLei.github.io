---
title: Kubernetes服务发现的原理和实例
published: true
---

### [](#header-3)介绍：

Kubernetes默认会自动配置内部DNS服务，提供轻量级的服务发现。

在Kubernetes 1.11版本之前，DNS服务是基于kube-dns的，在版本1.11的时候引入CoreDNS解决kube-dns所面临的的一些安全和稳定性问题。


### [](#header-3)Kubernetes服务发现原理：

无论使用什么软件处理DNS记录，都是按照如下的原理进行的：

1、 一个名为kube-dns的服务（1个或多个pod）被创建

2、 kube-dns服务从Kubernetes API中监听service和endpoint事件，并根据需要更新dns服务。这些事件会在你创建、更新或者删除Kubernetes服务和一直对应的pods时被触发。

3、 kubelet设置新pods的/etc/resolv.conf文件中的nameserver参数为kube-dns服务的IP地址，同时设置search选项允许短的hostnames被使用。

4、 在容器中运行的就可以解析hostnames到ip地址。


### [](#header-3)DNS域名结构：

#### [](#header-4)Kubernetes DNS记录实例

Kubernetes 服务的全名是这样的：_service.namespace.svc.cluster.local_

Kubernetes pod是这样的：_10.32.0.125.namespace.pod.cluster.local_

Kubernetes 服务命名端口的SRV记录是这样的： _port-name.protocol.service.namespace.svc.cluster.local_


上面的所有的Kubernetes自建的，在集群中的应用和微服务可以通着这些DNS名字访问对应的服务。

Kubernetes集群中的pod的resolv.conf文件如下所示：

```
  nameserver 10.96.0.10
  search default.svc.cluster.local svc.cluster.local cluster.local
  options ndots:5
```


#### [](#header-4)使用短域名解析服务

如上，在resolv.conf中列出了需要搜索的一些后缀，所以不需要在使用全名去搜索其他服务。

如果你需要的解析的服务在一个同一个命名空间中，你可以仅仅使用服务名：_other-service_

如果服务在不同的命名空间中，可以使用如下名字：_other-service.other-namespace_

如果目标是pod，最少需要如下名字：_pod-ip.other-namespace.pod_


### [](#header-3)Kubernetes DNS实现的一些细节

在Kubernetes1.11版本之前kube-dns服务由是三个容器组成，它们都在kube-systme命中空间中kube-dns pod中，这三个容器分别是：

1、 kube-dns：运行SkyDNS，作用的DNS域名解析

2、 dnsmasq：一个轻量级的DNS解析器和缓存，缓存来自SkyDNS的响应。

3、 sidecar：负责服务健康检查和生成报告

Dnsmasq的安全漏洞和SkyDNS的扩展性问题致使替代系统CoreDNS的产生。

CoreDNS使用Go写的单进程，覆盖前面所有的版本。
在解决性能和安全相关问题基础之上，CoreDNS修复一些小bug和添加一些新特性：

*   解决存根域（stubDomains）和外部服务的不兼容

*   通过随机相关记录的顺序，提高基于DNS的round-robin负载均衡
新特性autopath，提供解析到外部主机名的DNS响应时间，和更智能检索resolv.conf中的域名后缀。

*   kube-dns无论pod是否存在都会会解析10.32.0.125.namespace.pod.cluster.local到10.32.0.125. CoreDNS有pod验证模式，只有在pod存在和对应的IP和对应的命名空间正确时才会解析成功。

### [](#header-3)自定义DNS解析

在spec下面的dnsConfig，可以自定义dns解析

```
apiVersion: v1
kind: Pod
metadata:
  namespace: example
  name: custom-dns
spec:
  containers:
    - name: example
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 203.0.113.44
    searches:
      - custom.dns.local
```

### [](#header-3)实例和测试

...


**参考**：
`https://www.digitalocean.com/community/tutorials/an-introduction-to-the-kubernetes-dns-service`


文本你可以**加粗**, _斜体_, 和~~删除~~ 或者`关键字`

[也可以加个链接](www.baidu.com)

# [](#header-1)标题一

## [](#header-2)标题二

> 这是一个块引用
>
> 足够重要的事都可以加块引用

### [](#header-3)标题三

```js
// Js 代码高亮展示
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby 代码高亮展示
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### [](#header-4)标题四

*   这是一个无序列表
*   这是一个无序列表
*   这是一个无序列表

##### [](#header-5)标题五

1.  这是一个有序列表
2.  这是一个有序列表
3.  这是一个有序列表

###### [](#header-6)标题六

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### 下面是一个分割线

* * *

### 下面是一个无序列表:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### 下面是一个有序列表:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### 下面是一个嵌套的列表

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### 小图

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### 大图

![](https://guides.github.com/activities/hello-world/branching.png)


### 使用HTML语义定义列表

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
很长的单行代码块，不应该折叠。而是可以拉动，足够长的行才可以拉动，太短的不行，下面那条就是太短
```

```
太短的代码块
```

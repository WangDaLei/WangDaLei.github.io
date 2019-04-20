---
title: 搭建基于Http点播的nginx服务器
published: true
---

1.  这是一个有序列表
    yum install automake autoconf make gcc gcc-c++
    yum install pcre pcre-devel
    yum install zlib zlib-devel
    yum install openssl openssl-devel
2.  这是一个有序列表
    wget http://nginx.org/download/nginx-1.14.2.tar.gz
    tar xvf nginx-1.14.2.tar.gz
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-http_flv_module --with-http_mp4_module --with-http_auth_request_module
    make
    make install
3.  这是一个有序列表

1、安装依赖文件
yum install automake autoconf make gcc gcc-c++
yum install pcre pcre-devel
yum install zlib zlib-devel
yum install openssl openssl-devel
2、获取,解压,编译nginx源码
wget http://nginx.org/download/nginx-1.14.2.tar.gz
tar xvf nginx-1.14.2.tar.gz
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-http_flv_module --with-http_mp4_module --with-http_auth_request_module
make
make install
3、创建软连接
删除原nginx, 创建软连接
cd /usr/sbin
ln -s /usr/local/nginx/sbin/nginx nginx 

4、修改nginx配置
cd /usr/local/nginx/conf
vim nginx.conf

替换nginx文件配置为nginx.conf

5. 启动
nginx
停止
kill -9 `ps aux|grep nginx| awk '{print $2}'`

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

---
title: 搭建基于Http点播的nginx服务器
published: true
---

### *   安装环境：CentOS 6.10

1.  安装依赖库

    ```sh
    yum install automake autoconf make gcc gcc-c++
    yum install pcre pcre-devel
    yum install zlib zlib-devel
    yum install openssl openssl-devel
    ```

2.  获取nginx源码并编译

    ```sh
    wget http://nginx.org/download/nginx-1.14.2.tar.gz
    tar xvf nginx-1.14.2.tar.gz
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-http_flv_module --with-http_mp4_module --with-http_auth_request_module
    make
    make install
    ```

3.  安装可执行文件到系统

    ```sh
    # 删除原nginx, 创建软连接
    cd /usr/sbin
    ln -s /usr/local/nginx/sbin/nginx nginx 
    ```

4.  配置nginx，具体信息见nginx配置

    ```sh
    cd /usr/local/nginx/conf
    vim nginx.conf

    # 替换nginx文件配置为nginx.conf
    ```

5.  启动和停止

    ```sh
    nginx
    # 停止
    kill -9 `ps aux|grep nginx| awk '{print $2}'`
    ```

6.  nginx配置详细
    ```
    worker_processes 8;

    events {
        worker_connections  1024;
    }

    http {
        include       mime.types;
        default_type  application/octet-stream;
        access_log off;
        sendfile_max_chunk 5120k;
        sendfile        on;
        tcp_nopush     on;
        keepalive_timeout  65;
        gzip  on;

        server {

            listen      80;
            server_name _;

        location = /auth {
            internal;
            proxy_pass http://apiv1.jibon.com/apis/auth/;
            proxy_pass_request_body off;
            proxy_set_header        Content-Length "";
            proxy_set_header        X-Original-URI $request_uri;
        }
        location ~ \.mp4$
        {
         auth_request /auth;
         # auth_request_set $auth_status $upstream_status;
         root /data/videos;
         mp4;
         mp4_buffer_size    10m;
         mp4_max_buffer_size  50m;
         # limit_rate_after 40m;
         # limit_rate 10000k;
         #    limit_conn perip 1;
        }
        }
    }
    ```

* * *
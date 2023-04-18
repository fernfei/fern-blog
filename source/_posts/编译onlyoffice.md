---
title: 编译onlyoffice
date: 2023-04-18 19:27:15
tags:
- onlyoffice
---

### 1.前言

如果你只是使用onlyoffice的基础功能不打算二次开发或者简单添加自己的需求等，那么你可以按照官网的Document-Server的教程使用即可

[DocumentServer安装及使用](https://api.onlyoffice.com/editors/basic)

简单介绍一下官网中的DocumentServer和DocumentBuilder的区别

前者提供Web服务的前后端，后者是编译好一个程序根据你写的代码生成word等其他文档。我觉得前者应该是用于协同在线的文档，而后者是一个文档生成器帮你生成模版。

### 2.使用

#### 2.1.仅基于开源代码的功能

1.使用docker安装DocumentServer

``` shell
docker pull onlyoffice/documentserver:7.3
```

2.启动DocumentServer

``` shell
sudo docker run -i -t -d -p 9700:80 -p 9800:8000 \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/logs:/var/log/onlyoffice  \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data  \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/lib:/var/lib/onlyoffice \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/rabbitmq:/var/lib/rabbitmq \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/redis:/var/lib/redis \
    -v /Users/hufei/Desktop/onlyoffice/DocumentServer/db:/var/lib/postgresql  onlyoffice/documentserver:7.3
```

3.代码

``` html
<!DOCTYPE html>
<html>
<head>
    <title>ONLYOFFICE Documents</title>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=9"/>
    <meta name="description" content="" />
    <meta name="keywords" content="" />
    <script src="http://localhost:9700/web-apps/apps/api/documents/api.js"></script>
</head>
<body style="width: 100vw;height: 100vh;">
    <div id="onlyoffice"></div>
</body>
</html>

<script type="text/javascript">
    (function (){
        let config = {
            'id-view' : "onlyoffice",
            width: "100%",
            height: "100%",
            document: {
                fileType: "docx",
                key: "unique",
                title: "test.docx",
                url: "http://192.168.10.2:8080/test.docx",
            },
            documentType: "word",
            editorConfig: {
                "mode": "edit",
                lang: 'zh-CN',
                user: {
                    id: "123",
                    name: "user",
                    group: "Group1",
                },
            },
        };
        var docEditor = new DocsAPI.DocEditor("onlyoffice", config)
    })();
</script>

```

#### 2.2.编译Onlyoffice

开源的版本是有20人数限制想提升人数两种方式：

- [购买企业版](https://www.onlyoffice.com/docs-enterprise-prices.aspx?utm_source=github&amp;amp;utm_medium=cpc&amp;amp;utm_campaign=GitHubDS)

- 修改源码并编译

> onlyoffice社区版Community Server是根据GNU Affero通用公共许可证发行的。根据AGPL许可协议，在遵守AGPL许可协议的前提下，任何人都可以自由修改、使用、分发。

下面介绍编译过程

**温馨提示：编译Onlyoffice请严格按照如下要求进行否则中途必出问题，因为如果是arm版本和网络会出现很多莫名其妙的问题。**

- 系统：Unbuntu16.04

- CPU：Amd 6核（大于四核编译时CPU占用高）

- 内存：8G内存
- 硬盘：40G

- 其他：服务器选择国外服务器并且把网速拉满避免编译时中断，编译完就可以不受以上限制。





服务器可以去阿里云选择按量收费，大概0.8/h使用完释放即可。

![image-20230413150214235](http://image.hi-hufei.com/typora/image-20230413150214235.png)

下载依赖

``` shell
sudo apt-get update
sudo apt-get install -y python git
```

构建Onlyoffice代码

1.下载构建工具

``` shell
git clone https://github.com/ONLYOFFICE/build_tools.git
```

2.切换至build_tools/tools/linuxas目录

``` shell
cd build_tools/tools/linux
```

3.开始构建

```shell
./automate.py server
```

4.修改人数限制

```
 cd ../../../server/Common/sources/
 vim constants.js
```

![image-20230413151807301](http://image.hi-hufei.com/typora/image-20230413151807301.png)

5.再次构建

```
cd build_tools/tools/linux
./automate.py server
```

等待构建完成切换至`../../out/linux_64/onlyoffice/documentserver/`目录即可

如果你没有编译成功进群下载我编译成功文件。

![image-20230418192617449](http://image.hi-hufei.com/typora/image-20230418192617449.png)



#### 2.3.运行编译后的程序

##### 2.3.1. 官方启动教程

[官方教程](https://helpcenter.onlyoffice.com/installation/docs-community-compile.aspx)

##### 2.3.2. 使用我编译好的版本

如果你上面未编译成功可以使用我提供编译好的版本，该版本基于OnlyOffice7.3版本编译（人数限制2000绝对够用了，测试是否生效开20个网页即可）。

1.下载后在压缩包所在目录打开命令行

``` shell
docker load --input documentserver.tar
```

2.启动

``` shell
docker run -i -t -d -p 9500:80 -p 9600:8000 -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/logs:/var/log/onlyoffice  -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/data:/var/www/onlyoffice/Data  -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/lib:/var/lib/onlyoffice -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/rabbitmq:/var/lib/rabbitmq -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/redis:/var/lib/redis -v /Users/hufei/Desktop/onlyoffice3/DocumentServer/db:/var/lib/postgresql  huf/documentserver:7.3
```

3.进入docker容器

``` shell
docker exec -it 镜像id bash
```

4.启动nginx

``` shell
nginx
```

5.启动supervisor

``` shell
service supervisor start
```

6.启动所有程序

``` shell
supervisorctl start all
```

至此docker版本已启动可以使用了 访问  http://127.0.0.1:9500/welcome



##### 2.3.3 我编译后整合到docker的过程

这部分我只是为了记录我的过程，如果你只是需要编译后的源代码直接使用2.3.2提供的docker使用就行了

1.docker安装ubuntu

``` shell
docker pull ubuntu:16.04
```

2.启动

``` shell
docker run -dit -p 9500:22 --name ubuntu ubuntu:16.04
```

3.进入容器

``` shell
docker exec -it ubuntu bash
```



4.安装编译工具和依赖库

安装nginx（带secure_link模块）

```shell
sudo apt-get install build-essential libpcre3 libpcre3-dev zlib1g-dev libssl-dev
```

5.下载 Nginx 源码

到 [nginx 官网](http://nginx.org/) 下载对应版本的源码，例如：

```shell
wget http://nginx.org/download/nginx-1.20.1.tar.gz
```

6.解压源码包

```shell
tar -zxvf nginx-1.20.1.tar.gz
```

7.配置编译选项

进入源码目录，执行以下命令进行配置：

```shell
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-stream --with-stream_ssl_module --with-http_secure_link_module

```

8.编译和安装

``` shell
make && sudo make install
```

9.移动到/usr/sbin/

``` shell
cp /usr/local/nginx/sbin/nginx /usr/sbin/nginx
```

10.验证安装

执行以下命令，如果能够显示 Nginx 版本号，说明安装成功：

``` shell
nginx -v
```

11.安装supervisor

``` shell
apt-get update
apt-get install -y sudo vim supervisor
```

12.启动supervisor

``` shell
service supervisor start
```

13.安装postgresql

``` shell
sudo apt-get install postgresql
service postgresql start
sudo -i -u postgres psql -c "CREATE DATABASE onlyoffice;"
sudo -i -u postgres psql -c "CREATE USER onlyoffice WITH password 'onlyoffice';"
sudo -i -u postgres psql -c "GRANT ALL privileges ON DATABASE onlyoffice TO onlyoffice;"
```

14.初始化数据库

``` shell
psql -hlocalhost -Uonlyoffice -d onlyoffice -f /var/www/onlyoffice/documentserver/server/schema/postgresql/createdb.sql
```

15.安装rabbitmq

``` shell
sudo apt-get install rabbitmq-server
service rabbitmq-server start
```

16.上传文件到ubuntu内

提示：目录必须一致，因为里面的配置文件都是我现在上传目录，如果不对启动不起来。

``` shell
docker cp /OnlyOffice编译后程序/out/linux_64/onlyoffice 镜像id:/var/www/
docker cp /OnlyOffice编译后程序/onlyoffice 镜像id:/etc/
docker cp /OnlyOffice编译后程序/supervisor/conf.d 镜像id:/etc/supervisor/
```

17.配置nginx

``` shell
cd /usr/local/nginx/conf
rm -rf nginx.conf
vim nginx.conf
```

Nginx.conf文件内容

``` shell
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

   # server {
   #     listen       80;
   #     server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

  #      location / {
  #          root   html;
  #          index  index.html index.htm;
  #      }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
 #       error_page   500 502 503 504  /50x.html;
 #       location = /50x.html {
 #           root   html;
 #       }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
#    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include /usr/local/nginx/conf/conf.d/*.conf;
}
```

``` shell
mkdir conf.d
mkdir includes
cd conf.d
sudo ln -s /etc/onlyoffice/documentserver/nginx/ds.conf /etc/nginx/conf.d/ds.conf
cd ../includes/
sudo ln -s /etc/onlyoffice/documentserver/nginx/includes/ds-common.conf ./ds-common.conf
sudo ln -s /etc/onlyoffice/documentserver/nginx/includes/ds-docservice.conf ds-docservice.conf
sudo ln -s /etc/onlyoffice/documentserver/nginx/includes/ds-mime.types.conf ds-mime.types.conf
sudo ln -s /etc/onlyoffice/documentserver/nginx/includes/ds-letsencrypt.conf ds-letsencrypt.conf
sudo ln -s /etc/onlyoffice/documentserver/nginx/includes/http-common.conf http-common.conf
sudo ln -s /etc/onlyoffice/documentserver-example/nginx/includes/ds-example.conf  ds-example.conf
```

18 创建日志文件

``` shell
mkdir -p /var/log/onlyoffice/documentserver/converter
mkdir -p /var/log/onlyoffice/documentserver/docservice
mkdir -p /var/log/onlyoffice/documentserver/metrics
mkdir -p /var/log/onlyoffice/documentserver-example

cd /var/log/onlyoffice/documentserver/converter
touch out.log
cd /var/log/onlyoffice/documentserver/docservice
touch out.log
cd /var/log/onlyoffice/documentserver/metrics
touch out.log
cd /var/log/onlyoffice/documentserver/documentserver-example
touch out.log
```

19.启动程序

``` shel
supervisorctl sart all
```




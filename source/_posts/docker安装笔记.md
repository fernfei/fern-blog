---
title: docker安装笔记
date: 2022-09-12 10:45:54
tags:
- 笔记
- Linux
- docker
---

### 1. yum安装docker

``` bash
yum install docker
```

### 2.docker安装镜像

#### 2.1 拉取mysql

``` bash
docker pull mysql
```

##### 2.1.1 创建文件夹

``` bash
mkdir -p /root/data/db/mysql/data
mkdir -p /root/data/db/mysql/conf
mkdir -p /root/data/db/mysql/log
touch /root/data/db/mysql/conf/my.cnf
```

##### 2.1.2启动mysql

``` bash
docker run -p 3307:3306 --name mysql8 --privileged=true \
-v /root/data/db/mysql/log:/var/log/mysql \
-v /root/data/db/mysql/data:/var/lib/mysql \
-v /root/data/db/mysql/conf/my.cnf:/etc/mysql/my.cnf \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql
```

#### 2.2拉取redis

``` bash
docker pull redis
```

##### 2.2.1创建文件夹

``` bash
mkdir -p /root/data/db/redis/data
touch /root/data/db/redis/redis.conf
```

redis.conf配置文件

```bash
#edis配置文件

# Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
daemonize no

# 指定Redis监听端口，默认端口为6379
port 6379

# 绑定的主机地址，不要绑定容器的本地127.0.0.1地址，因为这样就无法在容器外部访问
bind 0.0.0.0

#需要密码则打开
#requirepass hufei

# 持久化
appendonly yes
```

##### 2.2.2 启动redis

``` bash
docker run -p 6379:6379 --name redis --privileged=true \
-v /root/data/db/redis/redis.conf:/etc/redis/redis.conf \
-v /root/data/db/redis/data:/data \
-d redis redis-server /etc/redis/redis.conf
```

#### 2.3拉取gitlab

``` bash
# 搜索镜像
docker search gitlab-ce
```

![image-20220912114525993](http://image.hi-hufei.com/typora/image-20220912114525993.png)

```bash
# 因为我的电脑是arm版本的所以选择arm版本的镜像
docker pull docker.io/yrzr/gitlab-ce-arm64v8
```



##### 2.3.1 创建文件夹

``` bash
mkdir -p /root/data/gitlab/confg
mkdir -p /root/data/gitlab/log
mkdir -p /root/data/gitlab/data
```

##### 2.3.2启动gitlab

``` bash
docker run \
    --detach \
  	--restart always \
    --publish 8443:443 \
    --publish 8000:80 \
    --publish 8222:22 \
    --name gitlab \
    --privileged=true \
    -v /root/data/gitlab/etc/conf:/etc/gitlab:z \
    -v /root/data/gitlab/etc/logs:/var/log/gitlab:z \
    -v /root/data/gitlab/etc/data:/var/opt/gitlab:z \
    docker.io/yrzr/gitlab-ce-arm64v8
```

##### 2.3.3查看密码

``` 111bash
docker exec -it gitlab /bin/bash
# 查看密码 默认保存24小时 或者在/root/data/gitlab/etc/conf/initial_root_password
vi /etc/gitlab/initial_root_password
```

#### 2.4拉取jenkins

``` 
docker pull jenkins/jenkins
```

##### 2.4.1创建文件夹

``` bash
mkdir -p /root/data/jenkins/jenkins_home
touch /root/data/jenkins/run/docker.sock
```

##### 2.4.2启动jenkins

``` bash
docker run \
--name jenkins \
-u root \
--rm -d \
-p 6100:8080 \
-p 6200:50000 \
-v /root/data/jenkins/jenkins_home:/var/jenkins_home \
-v /root/data/jenkins/run/docker.sock:/var/run/docker.sock \
jenkins/jenkins 
```

### 3.关闭selinux

#### 3.1临时关闭

``` bash
[root@hufei redis]# getenforce
Enforcing
[root@hufei redis]# setenforce 0
[root@hufei redis]# getenforce
Permissive
```

#### 3.2永久关闭

``` bash
vim /etc/sysconfig/selinux
```

SELINUX=enforcing 改为 SELINUX=disabled


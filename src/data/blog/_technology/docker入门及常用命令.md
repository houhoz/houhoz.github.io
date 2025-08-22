---
title: docker入门及常用命令
author: Leohoo
pubDatetime: 2022-08-22 15:11:26
slug: docker-introduction-common-commands
featured: false
draft: false
tags:
  - Docker
description: Docker入门及常用命令，深入理解容器化技术在前端开发中的应用。
---

## Table of contents

```bash
docker run -d -p 80:80 docker/getting-started
# -d 后台运行
# -p 80:80 - 将主机的 80 端口映射到容器中的 80 端口
# docker/getting-started - 要使用的镜像

# 简写
docker run -dp 80:80 docker/getting-started

# 查看所有镜像
$ docker images -a
REPOSITORY               TAG       IMAGE ID       CREATED        SIZE
docker/getting-started   latest    3ba8f2ff0727   4 months ago   27.9MB

# 查看所有容器
$ docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                               NAMES
03c632a6151c   docker/getting-started   "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   hungry_benz

# 进去容器
$ docker exec -it 03c632a6151c /bin/sh
/ # ls
bin                   media                 srv
dev                   mnt                   sys
docker-entrypoint.d   opt                   tmp
docker-entrypoint.sh  proc                  usr
etc                   root                  var
home                  run
lib                   sbin
/ #

# 构建镜像
docker build -t getting-started .

# -t 镜像的标志
# . Dockerfile的目录

# 停止
docker stop <the-container-id>
# 停止之后删除
docker rm <the-container-id>

# 上面的两个命令二合一
docker rm -f <the-container-id>

docker tag : 标记本地镜像，将其归入某一仓库。
docker tag getting-started houhoz/getting-started
docker push houhoz/getting-started

docker tag vue-demo hub.houho.site/vue-demo

docker push hub.houho.site/vue-demo
# 查看
houhoz ~/Desktop/nginx/conf.d [18:22:54]
$ curl https://hub.houho.site/v2/_catalog
{"repositories":["vue-demo"]}

docker pull hub.houho.site/vue-demo

# 删除异常停止的docker容器
docker rm `docker ps -a | grep Exited | awk '{print $1}'`

# 删除名称或标签为none的镜像
docker rmi -f  `docker images | grep '<none>' | awk '{print $3}'`
```

### 镜像 images

```bash
# 拉取镜像
docker pull image

# 拉指定版本的镜像
docker pull image:tag

# 查看所有的镜像
docker images -a

# 删除镜像
docker rmi -f 镜像id

# 删除所有的镜像
docker rmi -f ${docker images -aq}

# 删除所有仓库名为 redis 的镜像：
docker image rm $(docker image ls -q redis)

# 删除所有在 mongo:3.2 之前的镜像：
docker image rm $(docker image ls -q -f before=mongo:3.2)
```

## 容器

### **启动容器**

```bash
# 运行容器
docker run [可选参数] image

--name="name" #容器名称
-d            #后台运行
-it           #进入容器查看内容
-P            #指定容器的端口 -P  8080:8080（主机端口：容器端口）

# 启动并进入容器
docker run -it centos /bin/bash
```

### **退出容器**

```bash
# 从容器中退回主机 并停止
exit

# 容器不停止退出
ctr + p + q
```

### *查看容器*

```bash
docker ps
   # 列出当前正在运行的容器
-a #列出正在运行的容器，带出历史运行过的容器
-n=? #列出最近创建的容器
-q  #只显示容器的编码
```

### **删除容器**

```bash
# 删除容器
docker rm  容器id  # 删除容器,不能删除正在运行的容器 如果要强制删除 rm -f
docker rm -f  ${docker ps -aq} # 删除所有的容器
```

### **启动 停止容器**

```bash
# 启动容器
docker start 容器id
# 重启容器
docker restart 容器id

# 停止当前正在运行的容器
docker stop 容器id

# 强制停止当前容器
docker kill 容器id
```

### **常用命令**

```bash
#后台启动容器
docker run -d 镜像名

# 日志
docker logs --help
# 要显示的日志条数
docker logs -tf --tail 10 容器id
#查看所有的日志
docker logs -tf 容器id

# 查看容器中进程信息
docker top 容器id

# 查看容器内部的信息
docker inspect 容器id

# 进入当前正在进行的容器
docker exec -it 容器id  #开启新的终端

# 正在执行当前的代码
docker attach 容器id  # 进入当前的终端 不会重新启动终端

# 从容器内拷贝文件到主机
docker cp e83f48f28562:/home/index.html /home
```

### docker 启动nginx

```bash
# 后台启动 -d
# -p 主机端口：docker端口
docker run -d --name nginx01 -p 3344:80 nginx

root ~ [13:26:37] C:125
# 进入容器
$ docker exec -it nginx01 /bin/bash
root@2a6836e15fb5:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@2a6836e15fb5:/# cd /etc/nginx/
root@2a6836e15fb5:/etc/nginx# ls
conf.d	fastcgi_params	mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@2a6836e15fb5:/etc/nginx# exit
exit

root ~ [13:33:33]
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS       PORTS                                   NAMES
2a6836e15fb5   nginx     "/docker-entrypoint.…"   3 hours ago   Up 3 hours   0.0.0.0:3344->80/tcp, :::3344->80/tcp   nginx01

root ~ [13:33:41]
# 停止容器
$ docker stop 2a6836e15fb5
2a6836e15fb5
```

安装mysql

```bash
docker run -p 3306:3306 --name mysql -v /Users/hyzhao/Data/mysql/conf:/etc/mysql/conf.d -v /Users/hyzhao/Data/mysql/logs:/logs -v /Users/hyzhao/Data/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7

create database school;
show databases;

```

安装mongo

`docker run -p 27017:27017 --name mongodb -v /Users/hyzhao/data/mongodata:/data/db -d mongo --auth`

创建管理员用户

`docker exec -it mongodb bash`

`mongosh`

`use admin`

```bash
db.createUser({
  user: "hyzhao",
  pwd: "123456",
  roles: [
    { role: "root", db: "admin"}
  ]
});
```

先创建一个超级管理员 然后开启身份验证`--auth`

---
layout: note
title: Docker
categories: [Docker]
description: Docker 基础
keywords: Docker
---

## 基础

```
docker version  #详细版本信息
docker -v       #简略版本信息
docker info     #系统信息，包括镜像和容器数量

docker images                       #查看所有镜像，可选项 -a/--all 显示所有（默认不含中间映像层），-q/--quiet 只显示 id
docker search mysql                 #搜索 mysql 镜像，其他镜像类似
docker pull mysql:5.7               #下载 mysql 版本镜像，不指定 5.7 时默认为 latest
docker rmi $(docker images -aq)     #删除所有镜像，可使用单个镜像 id 删除对应单个镜像，可选项 -f 为强制删除

docker ps                       #列出正在运行的容器，可选项 -a 显示所有容器（包含未运行容器）
docker start 843f98910778
docker stop 843f98910778
docker restart 843f98910778
docker rm 843f98910778          #删除容器，rm 后为对应容器 id。关于容器的操作命令，容器 id 和容器名基本通用

#运行 MySQL，-d 表示后台运行
docker run -d --name mysql_server -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 mysql

#进入运行中的容器，-it 表示交互式进入容器内
docker exec -it f90ba583db6d /bin/bash

```

初学 Docker 技术时参考视频 [Docker 最新超详细版教程通俗易懂](https://www.bilibili.com/video/BV1og4y1q7M4?from=search&seid=7858926379313694975&spm_id_from=333.337.0.0) 以及对应网络笔记 [Docker 快速入门总结笔记](https://blog.csdn.net/huangjhai/article/details/118854733)，后续更多知识点参考官方文档。
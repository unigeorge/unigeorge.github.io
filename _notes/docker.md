---
layout: note
title: Docker
categories: [Docker]
description: Docker 基础
keywords: Docker
---

## 基础

初学 Docker 技术时参考 [Docker 最新超详细版教程通俗易懂](https://www.bilibili.com/video/BV1og4y1q7M4?from=search&seid=7858926379313694975&spm_id_from=333.337.0.0) 以及 [Docker 快速入门总结笔记](https://its203.com/article/huangjhai/118854733)，后续更多知识点参考官方文档。

### 常用命令

```
docker version  # 详细版本信息
docker -v       # 简略版本信息
docker info     # 系统信息，包括镜像和容器数量

docker images                       # 查看所有镜像，可选项 -a/--all 显示所有（默认不含中间映像层），-q/--quiet 只显示 id
docker search mysql                 # 搜索 mysql 镜像，其他镜像类似
docker pull mysql:5.7               # 下载 mysql 版本镜像，不指定 5.7 时默认为 latest
docker rmi $(docker images -aq)     # 删除所有镜像，可使用单个镜像 id 删除对应单个镜像，可选项 -f 为强制删除

docker ps                       # 列出正在运行的容器，可选项 -a 显示所有容器（包含未运行容器）
docker start 843f98910778
docker stop 843f98910778
docker restart 843f98910778
docker rm 843f98910778          # 删除容器，rm 后为对应容器 id。关于容器的操作命令，容器 id 和容器名基本通用

# 运行 MySQL，-d 表示后台运行；可添加 -v 主机目录:容器目录 添加容器数据卷信息
# MySQL 
docker run -d --name mysql_server -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 mysql

# 进入运行中的容器，-it 表示交互式进入容器内
docker exec -it f90ba583db6d /bin/bash

docker inspect f90ba583db6d     # 查看容器元数据信息

docker cp 容器id:容器内路径 主机路径    # 拷贝
```

## 进阶命令

```
# 三种挂载方式  容器内路径后可添加 :ro 表示容器内只读，:rw 表示容器内可读写
-v 容器内路径
-v 卷名:容器内路径
-v 宿主机路径:容器内路径

docker volume ls                # 列出所有数据卷信息
docker volume inspect 数据卷名  # 列出指定数据卷详细信息

# --volumes-from 实现“数据卷容器”   共享数据
docker run -it --name mysql02 --volumes-from mysql01 -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 mysql

# DockerFile -> DockerImage -> DockerContainer
# 自定义 centos DockerFile
FROM scratch    # 指定基础镜像，多数基础镜像为 scratch
MAINTAINER      # 指定维护者信息
RUN
ADD
WORKDIR         # 设置工作目录，直接进入容器时的目录
VOLUME          # 设置卷
EXPOSE          # 暴露端口
CMD             # 指定启动时运行的命令（只有最后一个生效）
ENTRYPOINT      # 类似 CMD，可以追加，都会生效      CMD 与 ENTRYPOINT 的追加包括 run 命令最后跟的参数
ONBUILD 
COPY            # 将文件拷贝到镜像中
ENV             # 构建的时候设置环境变量

# 实例：构建 centos DockerFile，命名 mydockerfile
FROM centos
MAINTAINER george<george@example.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD echo $MYPATH
CMD ["ls", "-a"]    
CMD /bin/bash           # 运行容器时只有本行 CMD 生效

# 实例：使用构建的 DockerFile 生成镜像，最后的 . 表示当前目录
docker build -f mydockerfile -t mycentos:0.1 .

# 查看镜像制作过程
docker history 镜像id
```
---
layout: note
title: MongoDB
categories: [MongoDB]
description: MongoDB 基础
keywords: MongoDB
---

## 安装

使用 Docker 安装，建议指定版本 4.4（未指定将拉取最新版本镜像，可能出现启动时找不到 Mongo 路径错误）。

```bash
docker pull mongo:4.4                                           # 拉取 4.4 版本镜像
docker run -p 27017:27017 --name mongodb -d mongo:4.4 --auth    # 指定后台运行且需要密码访问容器服务
docker exec -it mongodb mongo admin                             # 交互式进入容器
```

然后在容器内的 MongoDB 创建用户并使用该用户进行后续操作。

```bash
db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
db.auth('admin', '123456');
```

## 基础使用

可参考官方文档和 [GitHub 上的一篇笔记](https://github.com/Vacricticy/mongodb_practice)。
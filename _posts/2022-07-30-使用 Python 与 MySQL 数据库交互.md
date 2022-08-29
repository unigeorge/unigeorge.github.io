---
layout: post
title: 使用 Python 与 MySQL 数据库交互
categories: [Python, MySQL]
description: Python 与 MySQL 交互心得
keywords: Python, MySQL
topmost: false
---

使用 Python 将数据写入 Mysql 中，重点关注“无则写入/有则更新”功能的实现。

## MySQL 背景介绍

MySQL 数据库写入数据有以下几种方式：

- `insert into`，基础用法，不再赘述。
- `insert ignore into`，在有唯一键的情况下使用，若相同唯一键的数据已存在，则不写入。
- `replace into`，在有唯一键的情况下使用，若相同唯一键的数据已存在，则系统会针对该条数据**先删除再写入**。
- `insert into t1 (c1, c2) values (1, 2) on duplicate key update c2 = 3`，在有唯一键的情况下使用，若相同唯一键的数据已存在，则系统会针对该条数据按照 update 后的语句**直接更新**。

## MySQL 建表

初始设计独立的 id 字段为自增主键，另有联合唯一约束 （name，id），建表语句如下：

``` SQL
DROP TABLE if exists `trainer01`;
CREATE TABLE `trainer01` (
    `id` int(4) NOT NULL AUTO_INCREMENT,
    `name` varchar(20) NOT NULL,
    `age` int(4) DEFAULT NULL,
    `pokemon` varchar(20) DEFAULT NULL,
    `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`),
UNIQUE KEY `uni_key` (`name`,`age`)
) default charset=utf8;
```

后发现此结构不便于 MySQL 操作（具体下文分析），故放弃 id 主键，将（name，age）作为联合主键约束，建表语句如下：

``` SQL
DROP TABLE if exists `trainer02`;
CREATE TABLE `trainer02` (
    `name` varchar(20) NOT NULL,
    `age` int(4) DEFAULT NULL,
    `pokemon` varchar(20) DEFAULT NULL,
    `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`name`,`age`)
) default charset=utf8;
```

## 写入/更新数据

初始直接使用 pandas.DataFrame 的对应函数，写入对应表 trainer01 即可。

```python
import pandas as pd
from sqlalchemy import create_engine
import resource.data_credential as dc   # import MySQL info. eg: host, user, password 

data = {
    'name': ['satoshi', 'kasumi', 'takeshi'],
    'age': [10, 9, 11],
    'pokemon': ['pikachu', 'psyduck', 'onix']
}

df = pd.DataFrame(data)
print(df)

engine = create_engine(dc.MYSQL_DB_URI, pool_pre_ping=True, pool_recycle=3600)
df.to_sql(name='trainer01', con=engine, if_exists='append', index=False, index_label=False)
```

但当再次插入与已有唯一约束相同的数据时，会直接报错提示唯一键重复。因此需要动态写入/更新数据。

利用 sqlalchemy 实现根据**主键**更新（注意与上文的**唯一键**不同，因此若要更新数据，必须指定主键，否则会报错），"唯一约束不重复"是更新数据的必要非充分条件（也可以没有唯一约束）。

```python
from sqlalchemy import create_engine, Integer, String, Column, Float, select
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

import resource.data_credential as dc

Base = declarative_base()
engine = create_engine(dc.MYSQL_DB_URI, pool_pre_ping=True, pool_recycle=3600)
DBSession = sessionmaker(bind=engine)

class Trainer(Base):
    __tablename__ = 'trainer02'
    __mapper_args__ = {"eager_defaults": True}
    id = Column(Integer, primary_key=True)
    name = Column(String(10))
    age = Column(Integer)
    pokemon = Column(String(10))

data = [
    Trainer(name='satoshi', age=10, pokemon='pikachu'),
    Trainer(name='kasumi', age=9, pokemon='psyduck'),
    Trainer(name='takeshi', age=11, pokemon='togepi')
]

session = DBSession()
    for trainer in data:
        session.merge(trainer)
session.commit()

session.close()
```

## 查询数据

使用 pymysql 模块实现数据查询。

```python
import pandas as pd
import pymysql
import resource.data_credential as dc

conf = dc.MYSQL_INFO.copy()
conf['database'] = 'pokemon_db'
conn = pymysql.connect(**conf)
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)
cursor.execute('select * from trainer02')
res = cursor.fetchall()
res_df = pd.DataFrame(res)
print(res_df)

cursor.close()
conn.close()
```
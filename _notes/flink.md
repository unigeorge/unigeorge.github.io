---
layout: note
title: Flink
categories: [Flink]
description: Flink 知识点
keywords: Flink
---

以 1.12 版本为例，记录一些 Flink 使用过程中的知识点。

## 语法

- FlinkSQL 在某种程度上不支持 `!=` ，使用时会报 `org.apache.flink.table.api.SqlParserException: SQL parse failed. Bang equal '!=' is not allowed under the current SQL conformance level` 异常，可使用 `<>` 代替。

## 函数

全量函数用法可参考 [Apache Flink 1.12 Documentation: System (Built-in) Functions](https://nightlies.apache.org/flink/flink-docs-release-1.12/dev/table/functions/systemFunctions.html)。

- 尝试实现类似 Hive 中 `concat_ws('-', collect_set(列名))` 的聚合后行转列的操作，发现 Flink 似乎不支持 `collect_set` 函数，但可以选择 `listagg(列名, 分隔符)` 直接完成转换和拼接。
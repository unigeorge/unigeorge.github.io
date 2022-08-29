---
layout: note
title: Hive
categories: [Hive]
description: Hive 内置函数
keywords: Hive, builtin function
---

## 内置函数

Hive-3.1.2 版本中内置了 289 个运算符及函数（以下简称函数），本节对这些内置函数的某些特殊点进行记录学习，建议与 [Apache Hive LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-HiveOperatorsandUser-DefinedFunctions(UDFs)) 结合使用。

### $sum0 函数
- `show functions;` 默认返回的第三个函数
- 查询函数描述，可以看出该函数应该是有默认值 0 的求和函数
  ```
  hive (default)> desc function extended '$sum0';
  OK
  tab_name
  $sum0(x) - Returns the sum of a set of numbers, zero if empty
  Function class:org.apache.hadoop.hive.ql.udf.genericGenericUDAFSumEmptyIsZero
  Function type:BUILTIN
  ```
- 使用该函数会报 **NoViableAltException** 异常，因此未成功，但搜索到[一篇博文](https://blog.csdn.net/m0_56218665/article/details/117597871)中有成功使用记录，怀疑可能与 Hive 版本有关
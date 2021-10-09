---
layout: post
title: 在 Scala 程序 main 方法中定义并调用函数的小坑
categories: [Scala]
description: 记录关于 Scala 的一个 Error
keywords: Scala, Error
topmost: false
---

在 Scala 程序中遇到了一个 Error: forward reference extends over definition of variable，研究后发现是在 main 方法中定义和调用函数的顺序及位置导致，找到了一些解决办法但暂未能分析出根本原因，有待日后探究。

## 问题复现

编译运行以下代码（Scala 2.12.11版本）：

```
object Test {
  def main(args: Array[String]): Unit = {
    var s: String = sayHi("xiaoming") //提示此行错误
    println(s)

    def sayHi(name: String): String = {
      s"Hi, $name"
    }
  }
}
```

IDEA 提示：

```
Error:(5, 21) forward reference extends over definition of variable s
    var s: String = sayHi("xiaoming")
```

## 解决

根据不同需求可有以下几种方法避免此 Error：

- 将函数定义在 main方法之外

  这也是网上推崇最多的办法，但部分人认为“函数不能定义在 main 方法中”，这样的观点是错误的。

```
object Test {
  def main(args: Array[String]): Unit = {
    var s: String = sayHi("xiaoming") //运行成功，且sayHi与main方法的定义先后顺序不影响结果
    println(s)
  }

  def sayHi(name: String): String = {
    s"Hi, $name"
  }
}
```

- 将 var 替换为 def

  可以参考一下[此问题](https://stackoverflow.com/questions/45654315/forward-reference-extends-over-definition-of-value)，本质是将 s 看作了一个函数而非变量。

```
object Test {
  def main(args: Array[String]): Unit = {
    def s: String = sayHi("xiaoming") //更改了定义关键字
    println(s)

    def sayHi(name: String): String = {
      s"Hi, $name"
    }
  }
}
```

- 先定义sayHi函数，再调用

  由于提示说明“向前的引用超过了变量的定义”，尝试将定义函数的代码提前，果然运行成功，这让人联想到C语言的相关特性。

```
object Test {
  def main(args: Array[String]): Unit = {
    def sayHi(name: String): String = { //先定义，再调用
      s"Hi, $name"
    }

    var s: String = sayHi("xiaoming")
    println(s)
  }
}
```

- 先给变量赋其他值，再调用函数给其赋值

  这里有点想不明白为什么，但可以确定其他条件不变时，这样做是有效的，可见此情况下定义变量与调用函数赋值不能在同一个语句中进行。

```
object Test {
  def main(args: Array[String]): Unit = {
    var s: String = null  //先定义赋其他值
    s = sayHi("xiaoming")   //再调用函数赋值，成功
    println(s)

    def sayHi(name: String): String = {
      s"Hi, $name"
    }
  }
}
```

- 声明变量为 lazy

  与上一条类似，通过 lazy 关键字延迟函数的执行，将定义变量与调用函数赋值分开（不过要注意使用 val 定义变量）。

```
object Test {
  def main(args: Array[String]): Unit = {
    lazy val s: String = sayHi("xiaoming") //添加 lazy 关键字
    println(s)

    def sayHi(name: String): String = {
      s"Hi, $name"
    }
  }
}
```
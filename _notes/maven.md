---
layout: note
title: Maven
categories: [Maven]
description: Maven 插件、依赖库、脚本
keywords: Maven, plugin, repository, script
---

本文档相关代码内，所涉及的版本号仅为举例，可以按需更换。

## 插件

本节记录 Maven 中可能会用到的插件，使用时要注意标签的嵌套关系。

### 依赖打包

使用 Maven 进行 package 操作时，默认不会将依赖一起打包，若运行环境缺少这些依赖则会出问题，可以通过使用插件解决。在 pom.xml 文件中添加以下代码：

```
<build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.3.2</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
        <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <configuration>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
            </configuration>
            <executions>
                <execution>
                    <id>make-assembly</id>
                    <phase>package</phase>
                    <goals>
                        <goal>single</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

但如果部份依赖已经存在于运行环境中，可能会造成冲突，可以在 pom.xml 文件相关依赖中添加 `<scope>provided</scope>` 更改依赖范围以解决此问题。

### 支持 Scala

Scala 源代码的打包插件（Maven 默认不会将 Scala 代码编译并打包），在 pom.xml 文件中添加以下代码（final 标签是可选的）：

```
<build>
    <finalName>Your-Jar-Name</finalName>
    <plugins>
        <plugin>
            <groupId>net.alchim31.maven</groupId>
            <artifactId>scala-maven-plugin</artifactId>
            <version>3.4.6</version>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>testCompile</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## 依赖库

本节记录 Maven 中一些小众依赖库。

### Scala 样例对象转换成为 JSON 字符串

Fastjson 库可以实现 Java 对象与 JSON 格式字符串之间的相互转换，但不支持 Scala 的样例对象，此时需要 json4s 库。在 pom.xml 文件中添加以下代码以导入依赖库：

```
<dependencies>
    <dependency>
        <groupId>org.json4s</groupId>
        <artifactId>json4s-native_2.12</artifactId>
        <version>3.5.4</version>
    </dependency>
</dependencies>
```

使用前导入单例对象：

```
import org.json4s.native.Serialization
```

注意，使用前要先设置隐式参数：

```
implicit val formats=org.json4s.DefaultFormats
```

然后通过调用 `Serialization.write(scalaInstance)` 就可以将对象 `scalaInstance` 转换成 JSON 字符串。

## 脚本

### 删除失败文件

Maven 下载依赖库 Jar 包失败后，不能自动重新下载，需要手动删除失败文件。Windows 系统也可运行包含以下代码的脚本解决问题，具体见代码内说明文字。

```
@echo off

@ ECHO.
@ ECHO.
@ ECHO.                              说    明
@ ECHO -----------------------------------------------------------------------
@ ECHO		  本文件是专门用来解决 maven 下载所需 jar 包失败的情况下
@ ECHO			    需要频繁删除本地仓库的问题的
@ ECHO	 本程序会将本文件所在目录下的所有后缀为 ".lastUpdated" 的文件全部删除
@ ECHO				      请慎用
@ ECHO		    用法：将本文件放入 maven 本地仓库下双击即可运行
@ ECHO -----------------------------------------------------------------------
@ ECHO.
pause

del /s *.lastUpdated

@ ECHO -----------------------------------------------------------------------
@ ECHO		  成功删除本文件夹下所有后缀为 ".lastUpdated" 的文件
@ ECHO -----------------------------------------------------------------------
@ ECHO.
pause
```
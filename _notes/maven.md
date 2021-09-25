---
layout: note
title: Maven
categories: [Maven]
description: Maven 插件
keywords: Maven, plugin
---

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

## 脚本

### 删除失败文件

Maven 下载依赖 Jar 包失败后，不能自动重新下载，需要手动删除失败文件。也可运行包含以下代码的脚本解决问题，具体见代码内说明文字。

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
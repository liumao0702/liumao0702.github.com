---
layout: post
title:  "Hadoop分布式集群环境的搭建"
date:   2014-02-11 19:24:23
categories: hadoop
tags: hadoop hdfs mapred 实训
excerpt: 记录Hadoop培训教材编写。
---

* content
{:toc}

MapReduce开发环境搭建
## 1.实验目的

熟悉开发MapReduce程序的流程。

熟悉使用Eclipse IDE开发MapReduce程序。

## 2.实验准备
Hadoop1.2.1环境正常启动，Eclipse已正确安装；熟练使用Java语言和Eclipse IDE；熟悉HDFS Shell命令操作；了解MapReduce编程框架。实验中要用到的工具和软件可在实验附属的文件中找到。
3.实验内容
3.1 WordCount示例程序命令行编译与运行
WordCount示例程序的源码以及对源码的解释可以在讲义第三章MapReduce编程初级中找到，本实验主要以WordCount程序为例，学习和掌握MapReduce程序在Hadoop集群上的开发、部署流程。
接下来，首先是介绍不使用Eclipse IDE，开发和部署MapReduce程序流程。
步骤一：编译程序
进入Hadoop安装目录，在src/examples/org/apache/hadoop/examples目录下，有很
多MapReduce的示例程序，找到WordCount.java文件。
在Hadoop安装目录下新建app目录（名称可自取），将WordCount.java程序复制
到此目录下。使用命令如下：

设置CLASSPATH环境变量
WordCount例子程序和其他MapReduce程序都会用到MapReduce框架提供的编程接口，这些接口以jar包的形式存放在Hadoop安装目录的lib目录下，其中最重要的文件为hadoop-core-1.2.1.jar。因此，我们应该设置Java编译搜索路径CLASSPATH包含此jar包。编辑~/.bashrc系统环境变量文件，修改CLASSPATH，修改如下：

保存并退出，关闭当前命令窗口，重启一个新的命令窗口，输入如下命令：



联系人
组织机构
活动／主题（筹款或者众筹）
表单（自定义表单）
邮件和短信（时时通信工具）
支付记录

http://lxi.me/ep0hv
表叔，试用了下灵析，感觉还不错，能想到的，没想到的功能都有，特别是表单自定义，还有报表统计，都不错，很实用
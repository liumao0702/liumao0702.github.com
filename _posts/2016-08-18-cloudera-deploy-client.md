---
layout: post
title:  "Cloudera Cluster Deploy Client Configuration failed"
date:   2016-08-18 21:00:00
categories: cloudera configuration
tags: cloudera configuration
excerpt: Deploy Client Configuration failed
---

* content
{:toc}

# Cloudera Cluster Deploy Client Configuration failed

通过cm界面配置好各个组件（hdfs,yarn,oozie,spark等）的配置后，下发配置到各个节点时，报如下错误：eploy Client Configuration failed

![deployClientConfigrationFailed](/img/deployclient.png)


cm用alternatives进行配置文件的版本控制，进入部署失败的节点的／var/lib/alternatives目录下，发现有大小为0的配置文件，

![alternatives](/img/alternatives.png)

删除这些大小为0的配置文件，重新部署下

另外，安装集群的时候，某节点不能纳入集群，mv /usr/bin/host /usr/bin/host.bak后重新安装，可以解决Connection refused问题，其实是DNS反向解析错误，不能正确解析Cloudera Manager Server主机名

Cdh安装中遇到“正在获取安装锁”的解决办法,进入/tmp 目录，ls -a查看，删除scm_prepare_node.*的文件，以及.scm_prepare_node.lock文件。
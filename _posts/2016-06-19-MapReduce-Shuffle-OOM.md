---
layout: post
title:  "MapReduce Shuffle OOM"
date:   2016-06-19 21:00:00
categories: cloudera hadoop mapreduce
tags: mapreduce shuffle OOM
excerpt: MapReduce-Shuffle-OOM"
---

* content
{:toc}

# mapreduce shuffle OOM

## 问题错误日志如下

Error: org.apache.hadoop.mapreduce.task.reduce.Shuffle$ShuffleError: error in shuffle in fetcher#8 at org.apache.hadoop.mapreduce.task.reduce.Shuffle.run(Shuffle.java:134) at org.apache.hadoop.mapred.ReduceTask.run(ReduceTask.java:376) at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:168) at java.security.AccessController.doPrivileged(Native Method) at javax.security.auth.Subject.doAs(Subject.java:415) at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1642) at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:163) Caused by: java.lang.OutOfMemoryError: Java heap space at org.apache.hadoop.io.BoundedByteArrayOutputStream.<init>(BoundedByteArrayOutputStream.java:56) at org.apache.hadoop.io.BoundedByteArrayOutputStream.<init>(BoundedByteArrayOutputStream.java:46) at org.apache.hadoop.mapreduce.task.reduce.InMemoryMapOutput.<init>(InMemoryMapOutput.java:63) at org.apache.hadoop.mapreduce.task.reduce.MergeManagerImpl.unconditionalReserve(MergeManagerImpl.java:303) at org.apache.hadoop.mapreduce.task.reduce.MergeManagerImpl.reserve(MergeManagerImpl.java:293) at org.apache.hadoop.mapreduce.task.reduce.Fetcher.copyMapOutput(Fetcher.java:511) at org.apache.hadoop.mapreduce.task.reduce.Fetcher.copyFromHost(Fetcher.java:329) at org.apache.hadoop.mapreduce.task.reduce.Fetcher.run(Fetcher.java:193)

## 回顾mapreduce过程如下

### MapSide

1. 根据spilt，每个map读取相应的数据（默认每个block对应一个split，对应一个map）
2. mmap函数处理
3. 分区(partition),按照key来分区，排序
4. 归约(combile,可选）
5. 写入磁盘，输出结果，输出的结果首先写入预定义的buffer缓冲区，达到一定的阀值刷新到磁盘

### Shuffle 
1. reduce从各个map端复制数据，复制的数据首先写入预定义的buffer缓冲区，达到一定的阀值刷新到磁盘
2. 并归排序，合并数据

### Reduce Side
1. reduce函数处理
2. 输出到HDFS

上面shuffle过程，reduce copy数据时，是从各个map并行去获取数据，并行度cloudera默认为10(mapreduce.reduce.shuffle.parallelcopies=10),shuffle最大占jvm(mapred.child.java.opts)内存的默认值为0.25（mapreduce.reduce.shuffle.memory.limit.percent=0.25），预定义shuffle缓冲区内存默认最大占shuffle分配内存的0.9（mapred.job.shuffle.input.buffer.percent＝0.9），当这三者相乘大于1,且copy的数据量大于mapred.child.java.opts的值时，就会出现上面问题中的oom.

## 问题解决如下
1. 修改mapred.job.shuffle.input.buffer.percent＝0.35,其实是用空间换时间，如下
![shuffle_oom](/img/shuffle_oom.png)
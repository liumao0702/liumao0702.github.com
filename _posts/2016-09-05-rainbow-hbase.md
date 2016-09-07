---
layout: post
title:  "Hbase彩虹表设计"
categories: HBase
tags:  rainbow hbase
---

* content
{:toc}

## 基于HBase的彩虹表MD5哈希密码解密

 - 集群 ：2台namenode（高可用），4台datanode，24核心，64G内存
 - 数据量 ：约6T（5.98T）
 - hbase组件 ：4个HRegionServer服务（每一个datanode上起一个HRegionServer）
 - 设计 ：
   1. 4个HRegionServer平均分担6T的数据，每个HRegionServer平均分担1.5T数据





   2. HRegionServer下每个Store大小为10G，每个HRegionServer管理的HRegion为150（1.5T/10G）个，所有HRegion为600（150*4）个
 - 相关参数配置 ：

|Property|Value|
|------------- |:-------------:|
|hbase.hregion.max.filesize|10G|
|hbase.hstore.blockingStoreFiles|10|
|hbase.hregion.memstore.flush.size|256|
|hfile.block.cache.size|0.2|
|hbase.regionserver.global.memstore.upperLimit/lowerLimit|0.4/0.38|
|HBase Master 的 Java 堆栈大小（字节）|4G|
|HBase RegionServer 的 Java 堆栈大小（字节）|4G|

  - 表设计（高表）

| | FamilyColumn|
|------------- |:-------------:|-------------:|
|Key|indentiy(Column)|-- | 
|575b7bfc9f51fde2fa2bb3599e29ec84（MD5密文）|1：863564021100236(Column：MD5明文)|--|
   
  - 建表语句

```
create 'rainbow','identity',{ NUMREGIONS => 600, SPLITALGO => 'HexStringSplit' }
alter 'rainbow', NAME => 'identity', VERSIONS => 1 
```

   - 其他
    - 时间版本设置为1
    - key为MD5值，天然均匀分布
    - 列名称为1，减少key的索引数据量
   - 时间复杂度
     - 如果它还在MemStore里

       O(1) 查找region  +  O(log n)用来在MemStore里定位KeyValue (n=平均一个HFile里KeyValue条目的数量)
     - 如果它不在MemStore里

       O(1) 查找region  +  O(log m)用来在HFile里查找正确的数据块 (m=HFile中的数据块（HFile Block）的数量) + O(K)(K=块中KeyValue条目的数量)

       
   - 测试（MEID RainBow）
     - 总记录数：113568996276
     - 请求响应时间：小于1s
   - 截图如下

![1](/img/1.png)
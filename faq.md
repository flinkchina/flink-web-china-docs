---
title: 常见问题解答
---
<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<hr />

以下是关于Flink项目的**常见**问题。  
如果您还有其他问题，请确保参考[文档]({{site.docs-stable}})或者 [社区提问]({{ site.baseurl }}/gettinghelp.html).
{% toc %}

# 常见问题

## Apache Flink仅仅用于(近)实时处理用例吗?
Flink是一个非常通用的系统，用于数据处理和数据驱动应用程序核心组件。这些数据流可以是实时数据流，也可以是存储的历史数据流。

例如，在Flink的视图中，文件是存储的字节流。正因为如此，Flink支持实时数据处理和应用程序，以及批处理应用程序。  

流可以是*无界的*(没有结束，事件不断地到来)，也可以是*有界的*(流有开始和结束)。例如，来自消息队列的Twitter提要或事件流通常是无界流，而来自文件的字节流是有界流。  

## 如果一切都是流，为什么Flink中有DataStream和DataSet API ?

有界流通常比无界流处理效率更高。处理(接近)实时的无界事件流要求系统能够立即对事件进行操作并产生中间结果(通常具有较低的延迟)。处理有界流通常不需要产生低延迟的结果，因为数据无论如何都是旧的(相对而言)。这使得Flink能够以一种简单而高效的方式处理数据。

*DataStream* 数据流API捕获无界和有界流的连续处理，其模型支持低延迟结果和对事件和时间(包括事件时间)的灵活反应。  

*DataSet*数据集API的技术通常可以加快有限数据流的处理。将来，社区计划将这些优化与DataStream API中的技术结合起来。  

## Flink与Hadoop堆栈有什么关系?

Flink独立于[Apache Hadoop](https://hadoop.apache.org/)，运行时不依赖于任何Hadoop。
然而，Flink与许多Hadoop组件集成得非常好，例如*HDFS*、*YARN*或*HBase*。  
当与这些组件一起运行时，Flink可以使用HDFS读取数据，或者写入结果和检查点/快照。  
Flink可以通过YARN轻松部署，并与YARN和HDFS Kerberos安全模块集成。  


## Flink还运行哪些堆栈?

用户在[Kubernetes](https://kubernetes.io)上运行Flink ，[Mesos](https://mesos.apache.org/)，
[Docker](https://www.docker.com/)，或甚至作为独立服务。  

## 使用Flink的先决条件是什么?

  -  您需要*Java 8*来运行Flink作业/应用程序。
  -  Scala API(可选)依赖于Scala 2.11
  -  高度可用且没有单点故障的设置需要[Apache ZooKeeper](https://zookeeper.apache.org/)。
  -  对于可以从故障中恢复的高可用流处理设置，Flink需要为检查点提供某种形式的分布式存储(HDFS / S3 / NFS / SAN / GFS / Kosmos / Ceph /…)。

## Flink支持什么规模?

用户在非常小的设置(少于5个节点)和1000个节点上以及TBs状态下运行Flink作业。

## Flink仅限于内存数据集吗?

对于DataStream API, Flink支持在配置RocksDB状态后端时使用大于内存的状态。
对于数据集API，所有操作(delta迭代除外)都可以扩展到主内存之外。

# 常见的错误消息
常见的错误消息列在[获取帮助]({{ site.baseurl }}/gettinghelp.html#got-an-error-message) 页面上。
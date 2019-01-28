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

The following questions are frequently asked with regard to the Flink project **in general**. 
以下是关于Flink项目的**常见**问题。
If you have further questions, make sure to consult the [documentation]({{site.docs-stable}}) or [ask the community]({{ site.baseurl }}/gettinghelp.html).
如果您还有其他问题，请确保参考[文档]({{site.docs-stable}})或者 [社区提问]({{ site.baseurl }}/gettinghelp.html).
{% toc %}


# 常见问题

## Is Apache Flink only for (near) real-time processing use cases?
Apache Flink仅仅用于(近)实时处理用例吗?
Flink is a very general system for data processing and data-driven applications with *data streams* as
the core building block. These data streams can be streams of real-time data, or stored streams of historic data.
Flink是一个非常通用的系统，用于数据处理和数据驱动应用程序核心组件。这些数据流可以是实时数据流，也可以是存储的历史数据流。
For example, in Flink's view a file is a stored stream of bytes. Because of that, Flink
supports both real-time data processing and applications, as well as batch processing applications.
例如，在Flink的视图中，文件是存储的字节流。正因为如此，Flink支持实时数据处理和应用程序，以及批处理应用程序。  

Streams can be *unbounded* (have no end, events continuously keep coming) or be *bounded* (streams have a beginning and an end). For example, a Twitter feed or a stream of events from a message queue are generally unbounded streams, whereas a stream of bytes from a file is a bounded stream.
流可以是*无界的*(没有结束，事件不断地到来)，也可以是*有界的*(流有开始和结束)。例如，来自消息队列的Twitter提要或事件流通常是无界流，而来自文件的字节流是有界流。
## If everything is a stream, why are there a DataStream and a DataSet API in Flink?
如果一切都是流，为什么Flink中有DataStream和DataSet API ?
Bounded streams are often more efficient to process than unbounded streams. Processing unbounded streams of events in (near) real-time requires the system to be able to immediately act on events and to produce intermediate results (often with low latency). Processing bounded streams usually does not require producing low latency results, because the data is a while old anyway (in relative terms). That allows Flink to process the data in a simple and more efficient way.
有界流通常比无界流处理效率更高。处理(接近)实时的无界事件流要求系统能够立即对事件进行操作并产生中间结果(通常具有较低的延迟)。处理有界流通常不需要产生低延迟的结果，因为数据无论如何都是旧的(相对而言)。这使得Flink能够以一种简单而高效的方式处理数据。

The *DataStream* API captures the continuous processing of unbounded and bounded streams, with a model that supports low latency results and flexible reaction to events and time (including event time).
*DataStream* API捕获无界和有界流的连续处理，其模型支持低延迟结果和对事件和时间(包括事件时间)的灵活反应。  

The *DataSet* API has techniques that often speed up the processing of bounded data streams. In the future, the community plans to combine these optimizations with the techniques in the DataStream API.
数据集API的技术通常可以加快有限数据流的处理。将来，社区计划将这些优化与DataStream API中的技术结合起来。  

## Flink与Hadoop堆栈有什么关系?

Flink is independent of [Apache Hadoop](https://hadoop.apache.org/) and runs without any Hadoop dependencies.

Flink独立于[Apache Hadoop](https://hadoop.apache.org/)，运行时不依赖于任何Hadoop。

However, Flink integrates very well with many Hadoop components, for example, *HDFS*, *YARN*, or *HBase*.
然而，Flink与许多Hadoop组件集成得非常好，例如*HDFS*、*YARN*或*HBase*。
When running together with these components, Flink can use HDFS to read data, or write results and checkpoints/snapshots.
当与这些组件一起运行时，Flink可以使用HDFS读取数据，或者写入结果和检查点/快照。
Flink can be easily deployed via YARN and integrates with the YARN and HDFS Kerberos security modules.
Flink可以通过YARN轻松部署，并与YARN和HDFS Kerberos安全模块集成。


## Flink还运行哪些堆栈?

Users run Flink on [Kubernetes](https://kubernetes.io), [Mesos](https://mesos.apache.org/),
[Docker](https://www.docker.com/), or even as standalone services.
用户在[Kubernetes]上运行Flink (https://kubernetes.io)， [Mesos](https://mesos.apache.org/)，
(https://www.docker.com/)，或甚至作为独立服务。  

## 使用Flink的先决条件是什么?

  - You need *Java 8* to run Flink jobs/applications. 您需要*Java 8*来运行Flink作业/应用程序。
  - The Scala API (optional) depends on Scala 2.11. Scala API(可选)依赖于Scala 2.11
  - Highly-available setups with no single point of failure require [Apache ZooKeeper](https://zookeeper.apache.org/). 高度可用且没有单点故障的设置需要[Apache ZooKeeper](https://zookeeper.apache.org/)。
  - For highly-available stream processing setups that can recover from failures, Flink requires some form of distributed storage for checkpoints (HDFS / S3 / NFS / SAN / GFS / Kosmos / Ceph / ...). 对于可以从故障中恢复的高可用流处理设置，Flink需要为检查点提供某种形式的分布式存储(HDFS / S3 / NFS / SAN / GFS / Kosmos / Ceph /…)。

## Flink支持什么规模?

Users are running Flink jobs both in very small setups (fewer than 5 nodes) and on 1000s of nodes and with TBs of state.
用户在非常小的设置(少于5个节点)和1000个节点上以及TBs状态下运行Flink作业。
## Flink仅限于内存数据集吗?

For the DataStream API, Flink supports larger-than-memory state be configuring the RocksDB state backend.
对于DataStream API, Flink支持在配置RocksDB状态后端时使用大于内存的状态。
For the DataSet API, all operations (except delta-iterations) can scale beyond main memory.
对于数据集API，所有操作(delta迭代除外)都可以扩展到主内存之外。
# 常见的错误消息

Common error messages are listed on the [Getting Help]({{ site.baseurl }}/gettinghelp.html#got-an-error-message) page.
常见的错误消息列在[获取帮助]({{ site.baseurl }}/gettinghelp.html#got-an-error-message) 页面上。
---
title: 用例
---

<hr />

Apache Flink由于其广泛的特性集，是开发和运行许多不同类型应用程序的优秀选择。Flink的特性包括对流和批处理的支持、复杂的状态管理、事件时间处理语义，以及状态的精确一次一致性保证。此外，Flink可以部署在各种资源提供者上，如YARN、Apache Mesos和Kubernetes，也可以作为裸金属硬件上的独立集群。Flink配置为高可用性，没有单点故障。Flink已经被证明可以扩展到数千个内核和tb级的应用程序状态，提供高吞吐量和低延迟，并支持世界上一些最苛刻的流处理应用程序。

下面，我们将探索由Flink支持的最常见的应用程序类型，并给出实际示例的指针。
* <a href="#eventDrivenApps">事件驱动程序</a>
* <a href="#analytics">数据分析程序</a>
* <a href="#pipelines">数据管道应用程序</a>
  
## 事件驱动程序 <a name="eventDrivenApps"></a>

### 什么是event-driven事件驱动程序

An event-driven application is a stateful application that ingest events from one or more event streams and reacts to incoming events by triggering computations, state updates, or external actions.
事件驱动的应用程序是有状态的应用程序，它从一个或多个事件流中摄取事件，并通过触发计算、状态更新或外部操作对传入的事件作出响应。

Event-driven applications are an evolution of the traditional application design with separated compute and data storage tiers. In this architecture, applications read data from and persist data to a remote transactional database.
事件驱动的应用程序是传统应用程序设计的演化，它具有独立的计算和数据存储层。在此体系结构中，应用程序从远程事务数据库读取数据并将数据持久化到该数据库中。

In contrast, event-driven applications are based on stateful stream processing applications. In this design, data and computation are co-located, which yields local (in-memory or disk) data access. Fault-tolerance is achieved by periodically writing checkpoints to a remote persistent storage. The figure below depicts the difference between the traditional application architecture and event-driven applications.
相反，事件驱动的应用程序基于有状态流处理应用程序。在这种设计中，数据和计算是共存的，这将产生本地(内存或磁盘)数据访问。容错是通过定期将检查点写入远程持久存储来实现的。下图描述了传统应用程序体系结构和事件驱动应用程序之间的差异。
<br>
<div class="row front-graphic">
  <img src="/img/usecases-eventdrivenapps.png" width="700px" />
</div>

### 事件驱动应用程序的优点是什么?
Instead of querying a remote database, event-driven applications access their data locally which yields better performance, both in terms of throughput and latency. The periodic checkpoints to a remote persistent storage can be asynchronously and incrementally done. Hence, the impact of checkpointing on the regular event processing is very small. However, the event-driven application design provides more benefits than just local data access. In the tiered architecture, it is common that multiple applications share the same database. Hence, any change of the database, such as changing the data layout due to an application update or scaling the service, needs to be coordinated. Since each event-driven application is responsible for its own data, changes to the data representation or scaling the application requires less coordination.事件驱动的应用程序不需要查询远程数据库，而是在本地访问它们的数据，从而在吞吐量和延迟方面获得更好的性能。远程持久存储的定期检查点可以异步和增量地完成。因此，检查点对常规事件处理的影响非常小。然而，事件驱动的应用程序设计提供的好处不仅仅是本地数据访问。在分层体系结构中，多个应用程序共享同一个数据库是很常见的。因此，需要协调数据库的任何更改，例如由于应用程序更新或扩展服务而更改数据布局。由于每个事件驱动的应用程序都要对自己的数据负责，因此更改数据表示形式或扩展应用程序所需的协调更少。

### Flink如何支持事件驱动的应用程序?

The limits of event-driven applications are defined by how well a stream processor can handle time and state. Many of Flink's outstanding features are centered around these concepts. Flink provides a rich set of state primitives that can manage very large data volumes (up to several terabytes) with exactly-once consistency guarantees. Moreover, Flink's support for event-time, highly customizable window logic, and fine-grained control of time as provided by the `ProcessFunction` enable the implementation of advanced business logic. Moreover, Flink features a library for Complex Event Processing (CEP) to detect patterns in data streams. 
事件驱动应用程序的限制由流处理器处理时间和状态的能力来定义。Flink的许多突出特性都围绕着这些概念。Flink提供了一组丰富的状态原语，这些原语可以管理非常大的数据量(最高可达tb)，并且具有精确的一次一致性保证。此外，Flink对事件时间、高度可定制的窗口逻辑以及由`ProcessFunction`提供的对时间的细粒度控制的支持支持高级业务逻辑的实现。此外，Flink还提供了一个用于复杂事件处理(CEP)的库来检测数据流中的模式。

However, Flink's outstanding feature for event-driven applications are savepoints. A savepoint a consistent state image that can be used as a starting point for compatible applications. Given a savepoint, an application can be updated or adapt its scale, or multiple versions of an application can be started for A/B testing.
然而，Flink对于事件驱动应用程序的突出特性是保存点。保存点可以用作兼容应用程序起点的一致状态映像。给定一个保存点，可以更新或调整应用程序的规模，或者可以启动应用程序的多个版本进行A/B测试。
### 什么是典型的事件驱动应用程序?

* <a href="https://sf-2017.flink-forward.org/kb_sessions/streaming-models-how-ing-adds-models-at-runtime-to-catch-fraudsters/">欺诈检测</a>
* <a href="https://sf-2017.flink-forward.org/kb_sessions/building-a-real-time-anomaly-detection-system-with-flink-mux/">异常检测</a>
* <a href="https://sf-2017.flink-forward.org/kb_sessions/dynamically-configured-stream-processing-using-flink-kafka/">基于规则的提醒</a> 
* <a href="https://jobs.zalando.com/tech/blog/complex-event-generation-for-business-process-monitoring-using-apache-flink/">业务流程监控</a>
* <a href="https://berlin-2017.flink-forward.org/kb_sessions/drivetribes-kappa-architecture-with-apache-flink/">Web应用程序(社交网络)</a>

## 数据分析应用程序<a name="analytics"></a>

### 什么是数据分析应用程序? 

Analytical jobs extract information and insight from raw data. Traditionally, analytics are performed as batch queries or applications on bounded data sets of recorded events. In order to incorporate the latest data into the result of the analysis, it has to be added to the analyzed data set and the query or application is rerun. The results are written to a storage system or emitted as reports.
分析性工作从原始数据中提取信息和洞察力。传统上，分析是作为批处理查询或应用程序在有限的记录事件数据集上执行的。为了将最新的数据合并到分析的结果中，必须将其添加到分析的数据集中，并重新运行查询或应用程序。结果被写入存储系统或作为报告发出。

With a sophisticated stream processing engine, analytics can also be performed in a real-time fashion. Instead of reading finite data sets, streaming queries or applications ingest real-time event streams and continuously produce and update results as events are consumed. The results are either written to an external database or maintained as internal state. Dashboard application can read the latest results from the external database or directly query the internal state of the application.
通过复杂的流处理引擎，还可以实时执行分析。流查询或应用程序不再读取有限的数据集，而是摄取实时事件流，并在使用事件时不断生成和更新结果。结果要么写入外部数据库，要么作为内部状态维护。Dashboard应用程序可以从外部数据库读取最新结果，也可以直接查询应用程序的内部状态。
Apache Flink supports streaming as well as batch analytical applications as shown in the figure below.
Apache Flink支持流处理和批处理分析应用程序，如下图所示。
<div class="row front-graphic">
  <img src="/img/usecases-analytics.png" width="700px" />
</div>

### 流分析应用程序的优点是什么?
The advantages of continuous streaming analytics compared to batch analytics are not limited to a much lower latency from events to insight due to elimination of periodic import and query execution. In contrast to batch queries, streaming queries do not have to deal with artificial boundaries in the input data which are caused by periodic imports and the bounded nature of the input. 
与批处理分析相比，连续流分析的优势并不局限于从事件到insight洞察的更低延迟，因为它消除了定期导入和查询执行。与批处理查询不同，流查询不需要处理输入数据中的人为边界，这些人为边界是由周期性导入和输入的有界性质造成的。
Another aspect is a simpler application architecture. A batch analytics pipeline consist of several independent components to periodically schedule data ingestion and query execution. Reliably operating such a pipeline is non-trivial because failures of one component affect the following steps of the pipeline. In contrast, a streaming analytics application which runs on a sophisticated stream processor like Flink incorporates all steps from data ingestions to continuous result computation. Therefore, it can rely on the engine's failure recovery mechanism.
另一个方面是更简单的应用程序架构。批处理分析管道由几个独立的组件组成，它们定期调度数据的摄入和查询执行。可靠地操作这样的管道并不容易，因为一个组件的故障会影响管道的以下步骤。相反，流分析应用程序运行在像Flink这样的复杂流处理器上，它集成了从数据输入到连续结果计算的所有步骤。因此，它可以依赖于发动机的故障恢复机制。
### Flink如何支持数据分析应用程序?

Flink provides very good support for continuous streaming as well as batch analytics. Specifically, it features an ANSI-compliant SQL interface with unified semantics for batch and streaming queries. SQL queries compute the same result regardless whether they are run on a static data set of recorded events or on a real-time event stream. Rich support for user-defined functions ensures that custom code can be executed in SQL queries. If even more custom logic is required, Flink's DataStream API or DataSet API provide more low-level control. Moreover, Flink's Gelly library provides algorithms and building blocks for large-scale and high-performance graph analytics on batch data sets.
Flink为连续流和批处理分析提供了非常好的支持。具体来说，它具有一个符合ansi的SQL接口，具有用于批处理和流查询的统一语义。无论SQL查询是在记录事件的静态数据集上运行，还是在实时事件流上运行，它们都会计算相同的结果。对用户定义函数的丰富支持确保定制代码可以在SQL查询中执行。如果需要更多的定制逻辑，Flink的DataStream API或DataSet API提供了更多的低级控制。此外，Flink的Gelly库为批量数据集的大规模高性能图形分析提供了算法和构建块。
### 什么是典型的数据分析应用程序?

* <a href="http://2016.flink-forward.org/kb_sessions/a-brief-history-of-time-with-apache-flink-real-time-monitoring-and-analysis-with-flink-kafka-hb/">电信网络质量监控</a>
* <a href="https://techblog.king.com/rbea-scalable-real-time-analytics-king/">产品更新分析 &amp; 实验评价</a> in mobile applications
* <a href="https://eng.uber.com/athenax/">Ad-hoc analysis of live data实时数据的即席分析</a> in consumer technology
*  大规模图分析

## 数据管道应用程序 <a name="pipelines"></a>

### 什么是数据管道？

Extract-transform-load (ETL) is a common approach to convert and move data between storage systems. Often ETL jobs are periodically triggered to copy data from from transactional database systems to an analytical database or a data warehouse. 
提取-转换-加载(Extract-transform-load, ETL)是在存储系统之间转换和移动数据的常用方法。通常ETL作业被周期性地触发，以将数据从事务数据库系统复制到分析数据库或数据仓库。


Data pipelines serve a similar purpose as ETL jobs. They transform and enrich data and can move it from one storage system to another. However, they operate in a continuous streaming mode instead of being periodically triggered. Hence, they are able to read records from sources that continuously produce data and move it with low latency to their destination. For example a data pipeline might monitor a file system directory for new files and write their data into an event log. Another application might materialize an event stream to a database or incrementally build and refine a search index.
数据管道的作用类似于ETL作业。它们转换和丰富数据，并可以将其从一个存储系统移动到另一个存储系统。然而，它们以连续流模式运行，而不是周期性触发。因此，它们能够从不断生成数据的源读取记录，并以较低的延迟将其移动到目的地。例如，数据管道可以监视文件系统目录中的新文件，并将其数据写入事件日志。另一个应用程序可能将事件流物化到数据库，或者增量地构建和细化搜索索引。
The figure below depicts the difference between periodic ETL jobs and continuous data pipelines.
下图描述了周期性ETL作业和连续数据管道之间的区别。
<div class="row front-graphic">
  <img src="/img/usecases-datapipelines.png" width="700px" />
</div>

### 数据管道的优点是什么?

The obvious advantage of continuous data pipelines over periodic ETL jobs is the reduced latency of moving data to its destination. Moreover, data pipelines are more versatile and can be employed for more use cases because they are able to continuously consume and emit data. 
与周期性ETL作业相比，连续数据管道的明显优势是减少了将数据移动到目标的延迟。此外，数据管道更加通用，可以用于更多的用例，因为它们能够连续地使用和发出数据。

### Flink如何支持数据管道?

Many common data transformation or enrichment tasks can be addressed by Flink's SQL interface (or Table API) and its support for user-defined functions. Data pipelines with more advanced requirements can be realized by using the DataStream API which is more generic. Flink provides a rich set of connectors to various storage systems such as Kafka, Kinesis, Elasticsearch, and JDBC database systems. It also features continuous sources for file systems that monitor directories and sinks that write files in a time-bucketed fashion.
许多常见的数据转换或充实任务可以通过Flink的SQL接口(或表API)及其对用户定义函数的支持来解决。具有更高级需求的数据管道可以通过使用更通用的DataStream API来实现。Flink为各种存储系统(如Kafka、Kinesis、Elasticsearch和JDBC数据库系统)提供了丰富的连接器集。它还为监控目录的文件系统和以时间嵌套方式写入文件的接收器提供连续源。
### 什么是典型的数据管道应用程序?
* <a href="https://data-artisans.com/blog/blink-flink-alibaba-search">实时搜索索引构建</a> in e-commerce
* <a href="https://jobs.zalando.com/tech/blog/apache-showdown-flink-vs.-spark/">持续ETL</a> in e-commerce 


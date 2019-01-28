---
title: 什么是Apache Flink
---

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      Architecture &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">Applications</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">Operations</a>
    </h2>
  </div>
</div>
<hr/>

Apache Flink is a framework and distributed processing engine for stateful computations over *unbounded and bounded* data streams. Flink has been designed to run in *all common cluster environments*, perform computations at *in-memory speed* and at *any scale*.
Apache Flink是一个框架和分布式处理引擎，用于在*无界和有界的*数据流上进行有状态计算。Flink被设计成在*所有通用集群环境*中运行，以*内存级别速度*和*任意规模*执行计算。
Here, we explain important aspects of Flink's architecture.
在这里，我们将解释Flink架构的重要方面。
<!--
<div class="row front-graphic">
  <img src="/img/flink-home-graphic-update3.png" width="800px" />
</div>
-->

## Process Unbounded and Bounded Data 处理无界和有界数据

Any kind of data is produced as a stream of events. Credit card transactions, sensor measurements, machine logs, or user interactions on a website or mobile application, all of these data are generated as a stream. 
任何类型的数据都是作为事件流生成的。信用卡交易、传感器测量、机器日志或网站或移动应用程序上的用户交互，所有这些数据都以流的形式生成。

Data can be processed as *unbounded* or *bounded* streams. 
数据可以作为*无界*或*有界*流处理。

1. **Unbounded streams** have a start but no defined end. They do not terminate and provide data as it is generated. Unbounded streams must be continuously processed, i.e., events must be promptly handled after they have been ingested. It is not possible to wait for all input data to arrive because the input is unbounded and will not be complete at any point in time. Processing unbounded data often requires that events are ingested in a specific order, such as the order in which events occurred, to be able to reason about result completeness.
2. **无界流**有开始但没有结束。它们不会在生成数据时终止并提供数据。无界流必须是持续处理的，例如，事件被摄入后必须立即处理。不可能等待所有输入数据到达，因为输入是无界的，在任何时间点都不会完成。处理无界数据常常需要按照特定的顺序摄取事件，例如事件发生的顺序，以便能够推断结果的完整性。


3. **Bounded streams** have a defined start and end. Bounded streams can be processed by ingesting all data before performing any computations. Ordered ingestion is not required to process bounded streams because a bounded data set can always be sorted. Processing of bounded streams is also known as batch processing.
   **有界流**有个定义的开始和结束。在执行任何计算之前，可以通过摄取所有数据来处理有界流。处理有界流不需要有序摄入，因为有界数据集总是可以排序的。有界流的处理也称为批处理。

<div class="row front-graphic">
  <img src="/img/bounded-unbounded.png" width="600px" />
</div>

**Apache Flink excels at processing unbounded and bounded data sets.** Precise control of time and state enable Flink's runtime to run any kind of application on unbounded streams. Bounded streams are internally processed by algorithms and data structures that are specifically designed for fixed sized data sets, yielding excellent performance. 
**Apache Flink擅长处理无界和有界数据集**。对时间和状态的精确控制使Flink的运行时能够在无界流上运行任何类型的应用程序。有界流由专门为固定大小的数据集设计的算法和数据结构在内部进行处理，从而产生优异的性能。

Convince yourself by exploring the [use cases]({{ site.baseurl }}/usecases.html) that have been built on top of Flink.
通过探索构建在Flink之上的[使用案例]({{ site.baseurl }}/usecases.html)来说服自己。

## Deploy Applications Anywhere 部署应用程序在任何地方

Apache Flink is a distributed system and requires compute resources in order to execute applications. Flink integrates with all common cluster resource managers such as [Hadoop YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Mesos](https://mesos.apache.org), and [Kubernetes](https://kubernetes.io/) but can also be setup to run as a stand-alone cluster.
Apache Flink是一个分布式系统，需要计算资源来执行应用程序。Flink集成了所有常见的集群资源管理器，如[Hadoop YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Apache Mesos](https://mesos.apache.org)和[Kubernetes](https://kubernetes.io/)，但也可以独立集群安装运行。

Flink is designed to work well each of the previously listed resource managers. This is achieved by resource-manager-specific deployment modes that allow Flink to interact with each resource manager in its idiomatic way. 
Flink被设计为能够很好地运行前面列出的每个资源管理器。这是通过特定于资源管理器的部署模式实现的，该部署模式允许Flink以其惯用方式与每个资源管理器交互。
When deploying a Flink application, Flink automatically identifies the required resources based on the application's configured parallelism and requests them from the resource manager. In case of a failure, Flink replaces the failed container by requesting new resources. All communication to submit or control an application happens via REST calls. This eases the integration of Flink in many environments. 
在部署Flink应用程序时，Flink会根据应用程序配置的并行性自动识别所需的资源，并从资源管理器请求这些资源。在失败的情况下，Flink通过请求新资源来替换失败的容器。提交或控制应用程序的所有通信都是通过REST调用进行的。这简化了Flink在许多环境中的集成。

<!-- Add this section once library deployment mode is supported. -->
<!--

Flink features two deployment modes for applications, the *framework mode* and the *library mode*.

* In the **framework deployment mode**, a client submits a Flink application against a running Flink service that takes care of executing the application. This is the common deployment model for most data processing frameworks, query engines, or database systems.

* In the **library deployment mode**, a Flink application is packaged together with the Flink master executables into a (Docker) image. Another job-independent image contains the Flink worker executables. When a container is started from the job image, the Flink master process is started and the embedded application is automatically loaded. Containers started from the worker image, bootstrap Flink worker processes which automatically connect to the master process. A container manager such as Kubernetes monitors the running containers and automatically restarts failed containers. In this mode, you don't have to setup and maintain a Flink service in your cluster. Instead you package Flink as a library with your application. This model is very popular for deploying microservices. 

<div class="row front-graphic">
  <img src="/img/deployment-modes.png" width="600px" />
</div>

-->

## Run Applications at any Scale 以任何规模运行应用程序

Flink is designed to run stateful streaming applications at any scale. Applications are parallelized into possibly thousands of tasks that are distributed and concurrently executed in a cluster. Therefore, an application can leverage virtually unlimited amounts of CPUs, main memory, disk and network IO. Moreover, Flink easily maintains very large application state. Its asynchronous and incremental checkpointing algorithm ensures minimal impact on processing latencies while guaranteeing exactly-once state consistency.Flink被设计成在任何规模上运行有状态流应用程序。应用程序被并行化为可能有数千个任务，这些任务在集群中分布并并发执行。因此，应用程序实际上可以利用无限数量的cpu、主内存、磁盘和网络IO。而且，Flink很容易维护非常大的应用状态。它的异步和增量检查点算法确保对处理延迟的影响最小，同时保证精确的一次(exactly-once)状态一致性。

[Users reported impressive scalability numbers]({{ site.baseurl }}/poweredby.html) for Flink applications running in their production environments, such as
[用户报告了令人印象深刻的可伸缩性数字]({{ site.baseurl }}/poweredby.html)用于在其生产环境中运行的Flink应用程序，如:

* applications processing **multiple trillions of events per day**,
* applications maintaining **multiple terabytes of state**, and
* applications **running on thousands of cores**.
* 应用处理**每天数万亿事件**，
* 应用维护**数tb状态**，
* 应用程序**运行在数千个内核上**。


## Leverage In-Memory Performance 利用内存性能

Stateful Flink applications are optimized for local state access. Task state is always maintained in memory or, if the state size exceeds the available memory, in access-efficient on-disk data structures. Hence, tasks perform all computations by accessing local, often in-memory, state yielding very low processing latencies. Flink guarantees exactly-once state consistency in case of failures by periodically and asynchronously checkpointing the local state to durable storage.
有状态Flink应用程序针对本地状态访问进行了优化。任务状态总是在内存中维护，如果状态大小超过可用内存，则在磁盘上访问有效的数据结构中维护。因此，任务通过访问本地(通常在内存中)状态来执行所有计算，从而产生非常低的处理延迟。Flink通过定期和异步地将本地状态检查点到持久存储，从而在出现故障时确保精确的一次状态一致性。

<div class="row front-graphic">
  <img src="/img/local-state.png" width="600px" />
</div>

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      架构 &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">应用程序</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">操作</a>
    </h2>
  </div>
</div>
<hr/>

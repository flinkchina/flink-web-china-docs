---
title: 什么是Apache Flink
---

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      Flink架构 &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">Flink应用程序</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">Flink运维</a>
    </h2>
  </div>
</div>
<hr/>

Apache Flink是一个框架和分布式处理引擎，用于在*无界和有界的*数据流上进行有状态计算。Flink被设计成在*所有通用集群环境*中运行，以*内存级别速度*和*任意规模*执行计算。
在这里，我们将解释Flink架构的重要方面。
<!-- <div class="row front-graphic"> -->
  <!-- <img src="/img/flink-home-graphic-update3.png" width="800px" /> -->
<!-- </div>  -->


## 处理无界和有界数据

任何类型的数据都是作为事件流生成的。信用卡交易、传感器测量、机器日志或网站或移动应用程序上的用户交互，所有这些数据都以流的形式生成。

数据可以作为*无界*或*有界*流处理。

1. **无界流**有开始但没有结束。它们不会在生成数据时终止并提供数据。无界流必须是持续处理的，例如，事件被摄入后必须立即处理。不可能等待所有输入数据到达，因为输入是无界的，在任何时间点都不会完成。处理无界数据常常需要按照特定的顺序摄取事件，例如事件发生的顺序，以便能够推断结果的完整性。


2. **有界流**有个定义的开始和结束。在执行任何计算之前，可以通过摄取所有数据来处理有界流。处理有界流不需要有序摄入，因为有界数据集总是可以排序的。有界流的处理也称为批处理。

<div class="row front-graphic">
  <img src="/img/bounded-unbounded.png" width="600px" />
</div>

**Apache Flink擅长处理无界和有界数据集**。对时间和状态的精确控制使Flink的运行时能够在无界流上运行任何类型的应用程序。有界流由专门为固定大小的数据集设计的算法和数据结构在内部进行处理，从而产生优异的性能。

通过探索构建在Flink之上的[使用案例]({{ site.baseurl }}/usecases.html)来说服自己。

## 部署应用程序在任何地方

Apache Flink是一个分布式系统，需要计算资源来执行应用程序。Flink集成了所有常见的集群资源管理器，如[Hadoop YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Apache Mesos](https://mesos.apache.org)和[Kubernetes](https://kubernetes.io/)，但也可以独立集群安装运行。


Flink被设计为能够很好地运行前面列出的每个资源管理器。这是通过特定于资源管理器的部署模式实现的，该部署模式允许Flink以其惯用方式与每个资源管理器交互。

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

## 以任何规模运行应用程序

Flink被设计成在任何规模上运行有状态流应用程序。应用程序被并行化为可能有数千个任务，这些任务在集群中分布并并发执行。因此，应用程序实际上可以利用无限数量的cpu、主内存、磁盘和网络IO。而且，Flink很容易维护非常大的应用状态。它的异步和增量检查点算法确保对处理延迟的影响最小，同时保证精确的一次(exactly-once)状态一致性。

[用户报告了令人印象深刻的可伸缩性数字]({{ site.baseurl }}/poweredby.html)用于在其生产环境中运行的Flink应用程序，如:

* 应用处理**每天数万亿事件**，
* 应用维护**数tb状态**，
* 应用程序**运行在数千个内核上**。


## 利用内存性能

有状态Flink应用程序针对本地状态访问进行了优化。任务状态总是在内存中维护，如果状态大小超过可用内存，则在磁盘上访问有效的数据结构中维护。因此，任务通过访问本地(通常在内存中)状态来执行所有计算，从而产生非常低的处理延迟。Flink通过定期和异步地将本地状态检查点到持久存储，从而在出现故障时确保精确的一次状态一致性。

<div class="row front-graphic">
  <img src="/img/local-state.png" width="600px" />
</div>

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      Flink架构 &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">Flink应用程序</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">Flink运维</a>
    </h2>
  </div>
</div>
<hr/>

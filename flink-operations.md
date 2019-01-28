---
title: 什么是Apache Flink
---

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      <a href="{{ site.baseurl }}/flink-architecture.html">Flink架构</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">Flink应用程序</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      Flink运维
    </h2>
  </div>
</div>
<hr/>

Apache Flink是一个用于在无界和有界数据流上进行有状态计算的框架。由于许多流应用程序被设计成以最小停机时间连续运行，因此流处理器必须提供出色的故障恢复，以及在应用程序运行时监视和维护它们的工具。

Apache Flink非常关注流处理的操作方面。在这里，我们将解释Flink的故障恢复机制，并介绍其管理和监督运行的应用程序的功能。

## 24/7不间断地运行应用程序

在分布式系统中，机器和处理故障是普遍存在的。像Flink这样的分布式流处理器必须从故障中恢复，才能全天候运行流应用程序。显然，这不仅意味着在出现故障后重新启动应用程序，还意味着确保其内部状态保持一致，以便应用程序可以继续处理，就像从未发生过故障一样。
Flink提供了几个特性，以确保应用程序保持运行和一致性:

* **Consistent Checkpoints 一致性检查点**: Flink的恢复机制基于应用程序状态的一致性检查点。如果出现故障，应用程序将重新启动，并从最新的检查点加载其状态。结合可重置流源，该特性可以保证*精确的一次状态一致性 exactly-once state consistency*。
* **Efficient Checkpoints 高效的检查点**: 如果应用程序维护TB级的状态，则应用程序的状态的检查点可能非常昂贵。Flink可以执行异步和增量检查点，以使检查点对应用程序延迟SLA的影响非常小。
* **End-to-End Exactly-Once 端到端的Exactly-Once**: Flink为特定的存储系统提供了事务接收器(sink)功能，这些事务接收器(sink)保证即使在出现故障的情况下，数据也只被准确地写入一次。
* **Integration with Cluster Managers 与集群管理器的集成**: Flink与集群管理器紧密集成，例如[Hadoop YARN](https://hadoop.apache.org),[Mesos](https://mesos.apache.org)或者[Kubernetes](https://kubernetes.io)。当一个流程失败时，将自动启动一个新流程来接管其工作。
* **High-Availability Setup 高可用安装配置**: Flink具有高可用性模式，消除了所有单点故障。HA模式基于[Apache ZooKeeper](https://zookeeper.apache.org)，这是一种久经考验的可靠分布式协调服务。

## 更新、迁移、挂起和恢复应用程序
需要维护支持关键业务服务的流应用程序。需要修复bug，需要实现改进或新特性。然而，更新有状态流应用程序并非易事。通常，不能简单地停止应用程序并重新启动一个固定或改进的版本，因为不能失去应用程序的状态。

Flink的`Savepoints保存点`是一个独特而强大的特性，它解决了更新有状态应用程序和许多其他相关挑战的问题。保存点是应用程序状态的一致快照，因此非常类似于检查点。但是与检查点不同的是，保存点需要手动触发，而不是在应用程序停止时自动删除。保存点可用于启动状态兼容的应用程序并初始化其状态。保存点支持以下特性:

* **Application Evolution 应用程序演变**: 保存点可用于开发应用程序。应用程序的固定或改进版本可以从应用程序以前版本的保存点重新启动。也可以从较早的时间点启动应用程序(假设存在这样的保存点)来修复有缺陷的版本所产生的错误结果。
* **Cluster Migration 集群迁移**: 使用保存点，可以将应用程序迁移(或克隆)到不同的集群。
* **Flink Version Updates Flink版本更新**: 可以使用保存点将应用程序迁移到新的Flink版本上运行。
* **Application Scaling 应用程序拓展**: 保存点可用于增加或减少应用程序的并行性。
* **A/B Tests and What-If Scenarios A/B测试和假设场景**: 通过从相同的保存点启动所有版本，可以比较应用程序的两个(或多个)不同版本的性能或质量。
* **Pause and Resume 暂停和恢复**: 应用程序可以通过获取一个保存点并停止它来暂停。在以后的任何时间点，应用程序都可以从保存点恢复。
* **Archiving 存档**: 保存点可以存档，以便能够将应用程序的状态重置到更早的时间点。

##  监视和控制应用程序

就像任何其他服务一样，连续运行的流应用程序需要被监视并集成到操作基础设施中，即，监视和记录组织的服务。监控有助于预测问题并提前做出反应。日志记录使根本原因分析能够调查故障。最后，控制正在运行的应用程序的容易访问的接口是一个重要的特性。

Flink与许多常见的日志记录和监视服务集成得很好，并提供了一个REST API来控制应用程序和查询信息。

* **Web UI**: Flink提供了一个web UI来检查、监视和调试正在运行的应用程序。它还可以用于提交执行或取消执行。
* **Logging**: Flink实现了流行的slf4j日志记录接口，并与日志框架[log4j](https://logging.apache.org/log4j/2.x/)或[logback](https://logback.qos.ch/)集成。
* **Metrics**: Flink提供了一个复杂的度量系统来收集和报告系统和用户定义的度量。度量标准可以导出到多个报告器，包括[JMX](https://en.wikipedia.org/wiki/Java_Management_Extensions)、Ganglia、[Graphite](https://graphiteapp.org/)、[Prometheus](https://prometheus.io/)、[StatsD](https://github.com/etsy/statsd)、[Datadog](https://www.datadoghq.com/)和[Slf4j](https://www.slf4j.org/)。
* **REST API**: Flink暴露REST API以提交新应用程序、获取正在运行的应用程序的保存点或取消应用程序。REST API还公开运行或完成的应用程序的元数据和收集的指标。

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      <a href="{{ site.baseurl }}/flink-architecture.html">Flink架构</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-applications.html">Flink应用程序</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      Flink运维
    </h2>
  </div>
</div>
<hr/>

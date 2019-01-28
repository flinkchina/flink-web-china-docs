---
title: 什么是Apache Flink
---

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      <a href="{{ site.baseurl }}/flink-architecture.html">Flink架构</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      Flink应用程序 &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">Flink操作</a>
    </h2>
  </div>
</div>
<hr/>

Apache Flink is a framework for stateful computations over unbounded and bounded data streams. Flink provides multiple APIs at different levels of abstraction and offers dedicated libraries for common use cases.
Apache Flink是一个用于在无界和有界数据流上进行有状态计算的框架。Flink在不同的抽象级别上提供了多个api，并为通用用例提供了专用的库。

Here, we present Flink's easy-to-use and expressive APIs and libraries.
在这里，我们展示了Flink易于使用和表达的API和库。

## 流式应用程序的组件

The types of applications that can be built with and executed by a stream processing framework are defined by how well the framework controls *streams*, *state*, and *time*. In the following, we describe these building blocks for stream processing applications and explain Flink's approaches to handle them.
由流处理框架构建和执行的应用程序类型由框架如何控制*streams流*，*state状态*和*time时间*来定义。接下来，我们将描述流处理应用程序的这些组件，并解释Flink处理它们的方法。

### Streams流

Obviously, streams are a fundamental aspect of stream processing. However, streams can have different characteristics that affect how a stream can and should be processed. Flink is a versatile processing framework that can handle any kind of stream.
显然，流是流处理的一个基本方面。然而，流可以具有不同的特性，这些特性会影响流可以并且应该如何处理。Flink是一个通用的处理框架，可以处理任何类型的流。(译者注: 例如汽车分手动挡和自动挡，发动机引擎是一样的)

* **Bounded** and **unbounded** streams: Streams can be unbounded or bounded, i.e., fixed-sized data sets. Flink has sophisticated features to process unbounded streams, but also dedicated operators to efficiently process bounded streams. 
* **有界**和**无界**流:流可以是无界或有界的，即，固定大小的数据集。Flink具有处理无界流的复杂特性，但也具有高效处理有界流的专用操作符。
* **Real-time** and **recorded** streams: All data are generated as streams. There are two ways to process the data. Processing it in real-time as it is generated or persisting the stream to a storage system, e.g., a file system or object store, and processed it later. Flink applications can process recorded or real-time streams.

### State 状态
**实时**和**记录**流:所有数据都以流的形式生成。有两种处理数据的方法。在生成流时实时处理它，或者将流持久化到存储系统(例如文件系统或对象存储)，然后再处理它。Flink应用程序可以处理记录的或实时的流。
Every non-trivial streaming application is stateful, i.e., only applications that apply transformations on individual events do not require state. Any application that runs basic business logic needs to remember events or intermediate results to access them at a later point in time, for example when the next event is received or after a specific time duration.
每个重要的流应用程序都是有状态的，即，只有在单个事件上应用转换的应用程序才不需要状态。任何运行基本业务逻辑的应用程序都需要记住事件或中间结果，以便在以后的某个时间点（例如，当接收到下一个事件或在特定的时间段之后）访问它们。
<div class="row front-graphic">
  <img src="/img/function-state.png" width="350px" />
</div>

Application state is a first-class citizen in Flink. You can see that by looking at all the features that Flink provides in the context of state handling.
应用状态是Flink中的一级公民。通过查看Flink在状态处理上下文中提供的所有特性，您可以看到这一点。

* **Multiple State Primitives**: Flink provides state primitives for different data structures, such as atomic values, lists, or maps. Developers can choose the state primitive that is most efficient based on the access pattern of the function.

* **多个状态原语**: Flink为不同的数据结构(如原子值、列表或映射)提供了状态原语。开发人员可以根据函数的访问模式选择最有效的状态原语。
* **Pluggable State Backends**: Application state is managed in and checkpointed by a pluggable state backend. Flink features different state backends that store state in memory or in [RocksDB](https://rocksdb.org/), an efficient embedded on-disk data store. Custom state backends can be plugged in as well.
* **可插拔状态后端**:应用程序状态由可插拔状态后端和检查点管理。Flink具有将状态存储在内存或[RocksDB](https://rocksdb.org/)中(这是一种高效的嵌入式磁盘数据存储)的不同状态后端。也可以插入自定义状态后端。
* **Exactly-once state consistency**: Flink's checkpointing and recovery algorithms guarantee the consistency of application state in case of a failure. Hence, failures are transparently handled and do not affect the correctness of an application.
* **精确一次 状态一致性**: Flink的检查点和恢复算法保证了在出现故障时应用程序状态的一致性。因此，故障是透明处理的，不会影响应用程序的正确性。
* **Very Large State**: Flink is able to maintain application state of several terabytes in size due to its asynchronous and incremental checkpoint algorithm.
* **可维护大规模状态**: 由于其异步和增量检查点算法，Flink能够维护TB级别大小的应用程序状态。
* **Scalable Applications**: Flink supports scaling of stateful applications by redistributing the state to more or fewer workers.
* **可扩展的应用程序**: Flink通过将状态重新分布到更多或更少的worker来支持有状态应用程序的可伸缩性。


### Time

Time is another important ingredient of streaming applications. Most event streams have inherent time semantics because each event is produced at a specific point in time. Moreover, many common stream computations are based on time, such as windows aggregations, sessionization, pattern detection, and time-based joins. An important aspect of stream processing is how an application measures time, i.e., the difference of event-time and processing-time.

Flink provides a rich set of time-related features.

* **Event-time Mode**: Applications that process streams with event-time semantics compute results based on timestamps of the events. Thereby, event-time processing allows for accurate and consistent results regardless whether recorded or real-time events are processed.
* **Watermark Support**: Flink employs watermarks to reason about time in event-time applications. Watermarks are also a flexible mechanism to trade-off the latency and completeness of results.
* **Late Data Handling**: When processing streams in event-time mode with watermarks, it can happen that a computation has been completed before all associated events have arrived. Such events are called late events. Flink features multiple options to handle late events, such as rerouting them via side outputs and updating previously completed results.
* **Processing-time Mode**: In addition to its event-time mode, Flink also supports processing-time semantics which performs computations as triggered by the wall-clock time of the processing machine. The processing-time mode can be suitable for certain applications with strict low-latency requirements that can tolerate approximate results.

## Layered APIs 分层API

Flink provides three layered APIs. Each API offers a different trade-off between conciseness and expressiveness and targets different use cases.
Flink提供了三层api。每个API在简洁性和表达性之间提供了不同的平衡，并针对不同的用例。

<div class="row front-graphic">
  <img src="/img/api-stack.png" width="500px" />
</div>

We briefly present each API, discuss its applications, and show a code example.
我们将简要介绍每个API，讨论其应用程序，并展示一个代码示例。
### The ProcessFunctions

[ProcessFunctions](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/process_function.html) are the most expressive function interfaces that Flink offers. Flink provides ProcessFunctions to process individual events from one or two input streams or events that were grouped in a window. ProcessFunctions provide fine-grained control over time and state. A ProcessFunction can arbitrarily modify its state and register timers that will trigger a callback function in the future. Hence, ProcessFunctions can implement complex per-event business logic as required for many [stateful event-driven applications]({{ site.baseurl }}/usecases.html#eventDrivenApps).

The following example shows a `KeyedProcessFunction` that operates on a `KeyedStream` and matches `START` and `END` events. When a `START` event is received, the function remembers its timestamp in state and registers a timer in four hours. If an `END` event is received before the timer fires, the function computes the duration between `END` and `START` event, clears the state, and returns the value. Otherwise, the timer just fires and clears the state.

{% highlight java %}
/**
 * Matches keyed START and END events and computes the difference between 
 * both elements' timestamps. The first String field is the key attribute, 
 * the second String attribute marks START and END events.
 */
public static class StartEndDuration
    extends KeyedProcessFunction<String, Tuple2<String, String>, Tuple2<String, Long>> {

  private ValueState<Long> startTime;

  @Override
  public void open(Configuration conf) {
    // obtain state handle
    startTime = getRuntimeContext()
      .getState(new ValueStateDescriptor<Long>("startTime", Long.class));
  }

  /** Called for each processed event. */
  @Override
  public void processElement(
      Tuple2<String, String> in,
      Context ctx,
      Collector<Tuple2<String, Long>> out) throws Exception {

    switch (in.f1) {
      case "START":
        // set the start time if we receive a start event.
        startTime.update(ctx.timestamp());
        // register a timer in four hours from the start event.
        ctx.timerService()
          .registerEventTimeTimer(ctx.timestamp() + 4 * 60 * 60 * 1000);
        break;
      case "END":
        // emit the duration between start and end event
        Long sTime = startTime.value();
        if (sTime != null) {
          out.collect(Tuple2.of(in.f0, ctx.timestamp() - sTime));
          // clear the state
          startTime.clear();
        }
      default:
        // do nothing
    }
  }

  /** Called when a timer fires. */
  @Override
  public void onTimer(
      long timestamp,
      OnTimerContext ctx,
      Collector<Tuple2<String, Long>> out) {

    // Timeout interval exceeded. Cleaning up the state.
    startTime.clear();
  }
}
{% endhighlight %}

The example illustrates the expressive power of the `KeyedProcessFunction` but also highlights that it is a rather verbose interface.

### The DataStream API

The [DataStream API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/datastream_api.html) provides primitives for many common stream processing operations, such as windowing, record-at-a-time transformations, and enriching events by querying an external data store. The DataStream API is available for Java and Scala and is based on functions, such as `map()`, `reduce()`, and `aggregate()`. Functions can be defined by extending interfaces or as Java or Scala lambda functions. 

The following example shows how to sessionize a clickstream and count the number of clicks per session.

{% highlight java %}
// a stream of website clicks
DataStream<Click> clicks = ...

DataStream<Tuple2<String, Long>> result = clicks
  // project clicks to userId and add a 1 for counting
  .map(
    // define function by implementing the MapFunction interface.
    new MapFunction<Click, Tuple2<String, Long>>() {
      @Override
      public Tuple2<String, Long> map(Click click) {
        return Tuple2.of(click.userId, 1L);
      }
    })
  // key by userId (field 0)
  .keyBy(0)
  // define session window with 30 minute gap
  .window(EventTimeSessionWindows.withGap(Time.minutes(30L)))
  // count clicks per session. Define function as lambda function.
  .reduce((a, b) -> Tuple2.of(a.f0, a.f1 + b.f1));
{% endhighlight %}

### SQL &amp; Table API

Flink features two relational APIs, the [Table API and SQL](https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/index.html). Both APIs are unified APIs for batch and stream processing, i.e., queries are executed with the same semantics on unbounded, real-time streams or bounded, recorded streams and produce the same results. The Table API and SQL leverage [Apache Calcite](https://calcite.apache.org) for parsing, validation, and query optimization. They can be seamlessly integrated with the DataStream and DataSet APIs and support user-defined scalar, aggregate, and table-valued functions. 

Flink's relational APIs are designed to ease the definition of [data analytics]({{ site.baseurl }}/usecases.html#analytics), [data pipelining, and ETL applications]({{ site.baseurl }}/usecases.html#pipelines).

The following example shows the SQL query to sessionize a clickstream and count the number of clicks per session. This is the same use case as in the example of the DataStream API.

~~~sql
SELECT userId, COUNT(*)
FROM clicks
GROUP BY SESSION(clicktime, INTERVAL '30' MINUTE), userId
~~~

## Libraries

Flink features several libraries for common data processing use cases. The libraries are typically embedded in an API and not fully self-contained. Hence, they can benefit from all features of the API and be integrated with other libraries.

* **[Complex Event Processing (CEP)](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/cep.html)**: Pattern detection is a very common use case for event stream processing. Flink's CEP library provides an API to specify patterns of events (think of regular expressions or state machines). The CEP library is integrated with Flink's DataStream API, such that patterns are evaluated on DataStreams. Applications for the CEP library include network intrusion detection, business process monitoring, and fraud detection. 
  
* **[DataSet API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/batch/index.html)**: The DataSet API is Flink's core API for batch processing applications. The primitives of the DataSet API include *map*, *reduce*, *(outer) join*, *co-group*, and *iterate*. All operations are backed by algorithms and data structures that operate on serialized data in memory and spill to disk if the data size exceed the memory budget. The data processing algorithms of Flink's DataSet API are inspired by traditional database operators, such as hybrid hash-join or external merge-sort.
  
* **[Gelly](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/index.html)**: Gelly is a library for scalable graph processing and analysis. Gelly is implemented on top of and integrated with the DataSet API. Hence, it benefits from its scalable and robust operators. Gelly features [built-in algorithms](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/library_methods.html), such as label propagation, triangle enumeration, and page rank, but provides also a [Graph API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/graph_api.html) that eases the implementation of custom graph algorithms.

<hr/>
<div class="row">
  <div class="col-sm-12" style="background-color: #f8f8f8;">
    <h2>
      <a href="{{ site.baseurl }}/flink-architecture.html">Architecture</a> &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      Applications &nbsp;
      <span class="glyphicon glyphicon-chevron-right"></span> &nbsp;
      <a href="{{ site.baseurl }}/flink-operations.html">Operations</a>
    </h2>
  </div>
</div>
<hr/>

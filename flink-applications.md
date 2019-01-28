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


Apache Flink是一个用于在无界和有界数据流上进行有状态计算的框架。Flink在不同的抽象级别上提供了多个api，并为通用用例提供了专用的库。

在这里，我们展示了Flink易于使用和表达的API和库。

## 流式应用程序的组件

由流处理框架构建和执行的应用程序类型由框架如何控制*streams流*，*state状态*和*time时间*来定义。接下来，我们将描述流处理应用程序的这些组件，并解释Flink处理它们的方法。

### Streams流

显然，流是流处理的一个基本方面。然而，流可以具有不同的特性，这些特性会影响流可以并且应该如何处理。Flink是一个通用的处理框架，可以处理任何类型的流。(译者注: 例如汽车分手动挡和自动挡，发动机引擎是一样的)

* **有界**和**无界**流:流可以是无界或有界的，即，固定大小的数据集。Flink具有处理无界流的复杂特性，但也具有高效处理有界流的专用操作符。
* **实时**和**记录**流:所有数据都以流的形式生成。有两种处理数据的方法。在生成流时实时处理它，或者将流持久化到存储系统(例如文件系统或对象存储)，然后再处理它。Flink应用程序可以处理记录的或实时的流。
### State 状态

每个重要的流应用程序都是有状态的，即，只有在单个事件上应用转换的应用程序才不需要状态。任何运行基本业务逻辑的应用程序都需要记住事件或中间结果，以便在以后的某个时间点（例如，当接收到下一个事件或在特定的时间段之后）访问它们。
<div class="row front-graphic">
  <img src="/img/function-state.png" width="350px" />
</div>

应用状态是Flink中的一级公民。通过查看Flink在状态处理上下文中提供的所有特性，您可以看到这一点。

* **多个状态原语**: Flink为不同的数据结构(如原子值、列表或映射)提供了状态原语。开发人员可以根据函数的访问模式选择最有效的状态原语。
* **可插拔状态后端**:应用程序状态由可插拔状态后端和检查点管理。Flink具有将状态存储在内存或[RocksDB](https://rocksdb.org/)中(这是一种高效的嵌入式磁盘数据存储)的不同状态后端。也可以插入自定义状态后端。
* **精确一次 状态一致性**: Flink的检查点和恢复算法保证了在出现故障时应用程序状态的一致性。因此，故障是透明处理的，不会影响应用程序的正确性。
* **可维护大规模状态**: 由于其异步和增量检查点算法，Flink能够维护TB级别大小的应用程序状态。
* **可扩展的应用程序**: Flink通过将状态重新分布到更多或更少的worker来支持有状态应用程序的可伸缩性。

### Time 时间

时间是流应用程序的另一个重要组成部分。大多数事件流都具有固有的时间语义，因为每个事件都是在特定的时间点生成的。此外，许多常见的流计算都是基于时间的，例如windows聚合、session会话化、模式检测和基于时间的连接。流处理的一个重要方面是应用程序如何度量时间，即，事件时间与处理时间的差异。

Flink提供了一组丰富的与时间相关的特性。

* **事件时间event-time模式**: 处理具有事件时间语义的流的应用程序**根据事件的时间戳计算结果**。因此，事件时间处理允许精确和一致的结果，无论记录(译者注:批处理)的或实时的事件被处理。
* **水印支持**: Flink在事件时间应用程序中使用水印对时间进行推理。水印也是一种灵活的机制来权衡结果的延迟和完整性。
* **延迟数据处理**:当使用水印在事件 - 时间模式下处理流时，可能发生在所有相关事件到达之前已完成计算。 这类事件被称为迟发事件。 Flink具有多个选项来处理延迟事件，例如通过侧面输出(side outputs)重新路由它们以及更新以前完成的结果。 
* **处理时间模式**: 除了事件时间模式之外，Flink还支持处理时间语义，该语义执行由处理机器的壁挂钟时间触发的计算。处理时间模式可以适用于某些具有严格的低延迟要求、能够容忍近似结果的应用程序。
  
## Layered APIs 分层API

Flink提供了三层api。每个API在简洁性和表达性之间提供了不同的平衡，并针对不同的用例。

<div class="row front-graphic">
  <img src="/img/api-stack.png" width="500px" />
</div>

我们将简要介绍每个API，讨论其应用程序，并展示一个代码示例。
### The ProcessFunctions 处理函数

[ProcessFunctions](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/process_function.html) are the most expressive function interfaces that Flink offers. Flink provides ProcessFunctions to process individual events from one or two input streams or events that were grouped in a window. ProcessFunctions provide fine-grained control over time and state. A ProcessFunction can arbitrarily modify its state and register timers that will trigger a callback function in the future. Hence, ProcessFunctions can implement complex per-event business logic as required for many [stateful event-driven applications]({{ site.baseurl }}/usecases.html#eventDrivenApps).
[ProcessFunctions](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/process_function.html)是Flink提供的最具表现力的函数接口。Flink提供processfunction来处理来自窗口中分组的一个或两个输入流或事件中的单个事件。ProcessFunctions提供对时间和状态的细粒度控制。ProcessFunction可以任意修改它的状态和注册计时器，这些计时器将在将来触发回调函数。因此，ProcessFunctions可以实现许多[有状态事件驱动的应用程序]({{ site.baseurl }}/usecases.html#eventDrivenApps)所需的复杂的每个事件业务逻辑。

The following example shows a `KeyedProcessFunction` that operates on a `KeyedStream` and matches `START` and `END` events. When a `START` event is received, the function remembers its timestamp in state and registers a timer in four hours. If an `END` event is received before the timer fires, the function computes the duration between `END` and `START` event, clears the state, and returns the value. Otherwise, the timer just fires and clears the state.
下面的示例显示了对`keyedstream`执行操作并匹配`start`和`end`事件的`keyedprocessfunction`。当收到`start`事件时，函数会记住其状态时间戳，并在四小时内注册一个计时器。如果在计时器触发之前收到`end`事件，函数将计算`end`和`start`事件之间的持续时间，清除状态并返回值。否则，计时器只会触发并清除状态。

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
该示例演示了`KeyedProcessFunction`的强大表现力，但也强调了它是一个相当冗长的接口。

### The DataStream API 数据流API

The [DataStream API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/datastream_api.html) provides primitives for many common stream processing operations, such as windowing, record-at-a-time transformations, and enriching events by querying an external data store. The DataStream API is available for Java and Scala and is based on functions, such as `map()`, `reduce()`, and `aggregate()`. Functions can be defined by extending interfaces or as Java or Scala lambda functions. 
[DataStream API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/datastream_api.html)为许多常见的流处理操作提供了原语，例如窗口化、一次记录转换和通过查询外部数据存储来丰富事件。DataStream API可用于Java和Scala，它基于`map()`、`reduce()`和`aggregate()`等函数。函数可以通过扩展接口定义，也可以作为Java或Scala lambda函数定义。
The following example shows how to sessionize a clickstream and count the number of clicks per session.
下面的示例展示了如何会话一个clickstream并计算每个会话的单击次数。
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

### SQL&Table API

Flink features two relational APIs, the [Table API and SQL](https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/index.html). Both APIs are unified APIs for batch and stream processing, i.e., queries are executed with the same semantics on unbounded, real-time streams or bounded, recorded streams and produce the same results. The Table API and SQL leverage [Apache Calcite](https://calcite.apache.org) for parsing, validation, and query optimization. They can be seamlessly integrated with the DataStream and DataSet APIs and support user-defined scalar, aggregate, and table-valued functions. 

Flink有两个关系型API， [Table API和SQL](https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/index.html)。这两个api都是用于批处理和流处理的统一api，即，查询在无界、实时流或有界、记录流上以相同的语义执行，并产生相同的结果。表API和SQL利用[Apache Calcite](https://calcite.apache.org)进行解析、验证和查询优化。它们可以与DataStream和DataSet api无缝集成，并支持用户定义的标量、聚合和表值函数。

Flink's relational APIs are designed to ease the definition of [data analytics]({{ site.baseurl }}/usecases.html#analytics), [data pipelining, and ETL applications]({{ site.baseurl }}/usecases.html#pipelines).
Flink的关系api旨在简化[数据分析]({{ site.baseurl }}/usecases.html#analytics)、[数据管道和ETL应用程序]({{ site.baseurl }}/usecases.html#pipelines)的定义。
The following example shows the SQL query to sessionize a clickstream and count the number of clicks per session. This is the same use case as in the example of the DataStream API.
下面的示例显示了用于会话一个clickstream的SQL查询，并计算每个会话的单击次数。这与DataStream API示例中的用例相同。
~~~sql
SELECT userId, COUNT(*)
FROM clicks
GROUP BY SESSION(clicktime, INTERVAL '30' MINUTE), userId
~~~

## Libraries 库

Flink features several libraries for common data processing use cases. The libraries are typically embedded in an API and not fully self-contained. Hence, they can benefit from all features of the API and be integrated with other libraries
Flink提供了几个用于公共数据处理用例的库。这些库通常嵌入在API中，并且不是完全自包含的。因此，它们可以受益于API的所有特性，并与其他库集成。

* **[Complex Event Processing (CEP)](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/cep.html)**: Pattern detection is a very common use case for event stream processing. Flink's CEP library provides an API to specify patterns of events (think of regular expressions or state machines). The CEP library is integrated with Flink's DataStream API, such that patterns are evaluated on DataStreams. Applications for the CEP library include network intrusion detection, business process monitoring, and fraud detection. 

* **[复杂事件处理(cep)](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/cep.html)**: 模式检测是事件流处理的一个非常常见的用例。Flink的CEP库提供了一个API来指定事件的模式(想想正则表达式或状态机)。CEP库与Flink的DataStream API集成，这样就可以在DataStream上计算模式。CEP库的应用程序包括网络入侵检测、业务流程监控和欺诈检测。

* **[DataSet API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/batch/index.html)**: The DataSet API is Flink's core API for batch processing applications. The primitives of the DataSet API include *map*, *reduce*, *(outer) join*, *co-group*, and *iterate*. All operations are backed by algorithms and data structures that operate on serialized data in memory and spill to disk if the data size exceed the memory budget. The data processing algorithms of Flink's DataSet API are inspired by traditional database operators, such as hybrid hash-join or external merge-sort.
  
* **[数据集API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/batch/index.html)**: The DataSet API is Flink's core API for batch processing applications. The primitives of the DataSet API include *map*, *reduce*, *(outer) join*, *co-group*, and *iterate*. 数据集API是Flink批处理应用程序的核心API。数据集API的原语包括*map*、*reduce*、*(外部)join*、*co-group*和*iterate*。
  
* **[Gelly](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/index.html)**: Gelly is a library for scalable graph processing and analysis. Gelly is implemented on top of and integrated with the DataSet API. Hence, it benefits from its scalable and robust operators. Gelly features [built-in algorithms](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/library_methods.html), such as label propagation, triangle enumeration, and page rank, but provides also a [Graph API](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/graph_api.html) that eases the implementation of custom graph algorithms.

* **[Gelly](https://ci.apache.org/projects/flink/flink-docs-stable/dev/libs/gelly/index.html)**: Gelly是一个用于可伸缩图形处理和分析的库。Gelly是在此基础上实现的，并与数据集API集成。因此，它受益于其可伸缩和健壮的操作符。Gelly提供了[内置算法](https://ci.apache.org/projects/flects/flink/flinkdocs-stable/dev/libs/gelly/library_methods.html)，比如标签传播、三角形枚举和页面排名，但也提供了[图Graph API](https://ci.apache.org/projects/flink/flinkdocs-stable/dev/libs/gelly/graph_api .html)，它简化了自定义图算法的实现。

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

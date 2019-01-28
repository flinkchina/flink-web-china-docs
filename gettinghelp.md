---
title: 获取帮助
---

<hr />

{% toc %}

## 有问题?

The Apache Flink community answers many user questions every day. You can search for answers and advice in the archives or reach out to the community for help and guidance.

Apache Flink社区每天都会回答许多用户问题。您可以在档案中查找答案和建议，或者向社区寻求帮助和指导。
### 用户邮件列表

Many Flink users, contributors, and committers are subscribed to Flink's user mailing list. The user mailing list is a very good place to ask for help. 
许多Flink用户、贡献者和提交者都订阅了Flink的用户邮件列表。用户邮件列表是寻求帮助的好地方。
Before posting to the mailing list, you can search the mailing list archives for email threads that discuss issues related to yours on the following websites.
在发布到邮件列表之前，您可以在邮件列表存档中搜索以下网站上讨论与您的邮件相关问题的电子邮件线程。
- [Apache Pony 邮件存档](https://lists.apache.org/list.html?user@flink.apache.org)
- [Nabble 存档](http://apache-flink-user-mailing-list-archive.2336050.n4.nabble.com/)

If you'd like to post to the mailing list, you need to
如果您想要邮寄到邮件列表中，您需要这样做
1. 通过发送电子邮件订阅邮件列表到 `user-subscribe@flink.apache.org`, 
2. 回复确认邮件确认订阅
3. 发送电子邮件至 `user@flink.apache.org`.

请注意，如果您没有订阅，您将不会收到邮件的回复。

### Stack Overflow

Many members of the Flink community are active on [Stack Overflow](https://stackoverflow.com). You can search for questions and answers or post your questions using the [\[apache-flink\]](https://stackoverflow.com/questions/tagged/apache-flink) tag. 
Flink社区的许多成员都活跃在[Stack Overflow](https://stackoverflow.com)上。您可以使用[\[apache-flink\]](https://stackoverflow.com/questions/tagged/apache-flink)标签搜索问题和答案，或者发布您的问题。
## 发现bug?

If you observe an unexpected behavior that might be caused by a bug, you can search for reported bugs or file a bug report in [Flink's JIRA](https://issues.apache.org/jira/issues/?jql=project %3D FLINK).
如果您观察到可能由错误引起的意外行为，您可以搜索报告的错误或在[Flink的JIRA](https://issues.apache.org/jira/issues/?jql=project %3D FLINK)中提交错误报告
If you are unsure whether the unexpected behavior happend due to a bug or not, please post a question to the [user mailing list](#user-mailing-list).
如果您不确定意外行为是否由错误引起，请向[用户邮件列表](#user-mailing-list)发送问题。
## 遇到错误信息？

Identifying the cause for an error message can be challenging. In the following, we list the most common error messages and explain how to handle them.
识别错误消息的原因可能具有挑战性。下面，我们将列出最常见的错误消息并解释如何处理它们。
### 我有一个NotSerializableException

Flink uses Java serialization to distribute copies of the application logic (the functions and operations you implement,
as well as the program configuration, etc.) to the parallel worker processes.
Because of that, all functions that you pass to the API must be serializable, as defined by
[java.io.Serializable](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html).
Flink使用Java序列化将应用程序逻辑(您实现的函数和操作，以及程序配置等)的副本分发到并行工作进程。因此，传递给API的所有函数都必须是可序列化的，正如[java.io.Serializable](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)所定义的那样  
If your function is an anonymous inner class, consider the following:
如果您的函数是一个匿名内部类，请考虑以下情况:  
  - 使函数成为独立类或静态内部类
  - 使用Java 8 lambda函数

Is your function is already a static class, check the fields that you assign when you create
an instance of the class. One of the fields most likely holds a non-serializable type.
如果函数已经是静态类，则在创建该类的实例时检查所分配的字段。其中一个字段很可能包含不可序列化类型。
  - 在Java中，使用`RichFunction`并在`open()`方法中初始化有问题的字段。
  - 在Scala中，您通常可以简单地使用`lazy val`来延迟初始化，直到分布式执行发生。这可能会带来较小的性能成本。当然，你也可以在Scala中使用`RichFunction`。

### 使用Scala API，我得到一个关于隐式值和证据参数的错误。

This error means that the implicit value for the type information could not be provided.
Make sure that you have an `import org.apache.flink.streaming.api.scala._` (DataStream API) or an
`import org.apache.flink.api.scala._` (DataSet API) statement in your code.
此错误意味着无法提供类型信息的隐式值。确保您的代码中有一个`import org.apache.flink.streaming.api.scala._`(DataStream API)或`import org.apache.flink.api.scala._`(DataSet API)语句。
If you are using Flink operations inside functions or classes that take
generic parameters, then a TypeInformation must be available for that parameter.
This can be achieved by using a context bound:
如果在接受泛型参数的函数或类中使用Flink操作，则必须为该参数提供类型信息。这可以通过使用上下文绑定来实现:
~~~scala
def myFunction[T: TypeInformation](input: DataSet[T]): DataSet[Seq[T]] = {
  input.reduceGroup( i => i.toSeq )
}
~~~

See [Type Extraction and Serialization]({{ site.docs-snapshot }}/dev/types_serialization.html) for
an in-depth discussion of how Flink handles types.
有关Flink如何处理类型的深入讨论，请参见[类型提取和序列化]({{ site.docs-snapshot }}/dev/types_serialization.html) 。
### 我看到一个ClassCastException: X不能被强制转换为X。

When you see an exception in the style `com.foo.X` cannot be cast to `com.foo.X` (or cannot be assigned to `com.foo.X`), it means that
multiple versions of the class `com.foo.X` have been loaded by different class loaders, and types of that class are attempted to be assigned to each other.
当您看到样式`com.foo.X`中的异常不能转换为`com.foo.X`(或不能分配给`com.foo.X`)时，这意味着类`com.foo.X`的多个版本已被不同的类加载器加载，并且该类的类型试图彼此分配。
The reason for that can be:
原因可能是:
  - Class duplication through `child-first` classloading. That is an intended mechanism to allow users to use different versions of the same
    dependencies that Flink uses. However, if different copies of these classes move between Flink's core and the user application code, such an exception
    can occur. To verify that this is the reason, try setting `classloader.resolve-order: parent-first` in the configuration.
    If that makes the error disappear, please write to the mailing list to check if that may be a bug.
通过`child-first` 类加载实现类复制。这是一种允许用户使用Flink使用的相同依赖项的不同版本的机制。但是，如果这些类的不同副本在Flink的核心和用户应用程序代码之间移动，就会出现这样的异常。要验证这就是原因，请尝试设置`classloader.resolve-order: parent-first` 。如果这使错误消失，请写信到邮件列表检查是否可能是一个错误。
  - Caching of classes from different execution attempts, for example by utilities like Guava’s Interners, or Avro's Schema cache.从不同的执行尝试缓存类，例如通过实用程序(如Guava的Interners)或Avro的模式缓存。
    Try to not use interners, or reduce the scope of the interner/cache to make sure a new cache is created whenever a new task
    execution is started 尽量不要使用interner，或者减少interner/cache的作用域，以确保在启动新任务执行时创建新缓存。 

### 我有AbstractMethodError或NoSuchFieldError。

Such errors typically indicate a mix-up in some dependency version. That means a different version of a dependency (a library)
is loaded during the execution compared to the version that code was compiled against.
这类错误通常表示在某些依赖项版本中出现了错误。这意味着在执行过程中加载的依赖项(库)版本与编译代码时加载的版本不同。

From Flink 1.4.0 on, dependencies in your application JAR file may have different versions compared to dependencies used by Flink's core, or other dependencies in the classpath (for example from Hadoop). That requires `child-first` classloading to be activated, which is the default.
从Flink 1.4.0开始，与Flink的核心使用的依赖项或类路径中的其他依赖项(例如Hadoop)相比，应用程序JAR文件中的依赖项可能具有不同的版本。这要求激活`child-first`类加载，这是默认的。

If you see these problems in Flink 1.4+, one of the following may be true:
如果您在Flink 1.4+中看到这些问题，那么以下情况之一可能是正确的:
  - You have a dependency version conflict within your application code. Make sure all your dependency versions are consistent.您的应用程序代码中存在依赖项版本冲突。确保所有依赖项版本都是一致的。
  - You are conflicting with a library that Flink cannot support via `child-first` classloading. Currently these are the Scala standard library classes, as well as Flink's own classes, logging APIs, and any Hadoop core classes.
您与Flink无法通过`child-first`类加载来支持的库存在冲突。目前这些是Scala标准库类，以及Flink自己的类、日志api和任何Hadoop核心类。

### My DataStream application produces no output, even though events are going in.
我的DataStream应用程序不会产生任何输出，即使事件正在进入。

If your DataStream application uses *Event Time*, check that your watermarks get updated. If no watermarks are produced, event time windows might never trigger, and the application would produce no results.
如果您的DataStream应用程序使用*事件时间*，请检查您的水印是否得到更新。如果没有生成水印，事件时间窗口可能永远不会触发，应用程序也不会产生结果。
You can check in Flink's web UI (watermarks section) whether watermarks are making progress.
您可以在Flink的web UI(水印部分)中检查水印是否正在取得进展。
### 我看到一个异常报告"Insufficient number of network buffers"(网络缓冲区数量不足)。
If you run Flink with a very high parallelism, you may need to increase the number of network buffers.
如果您以非常高的并行度运行Flink，可能需要增加网络缓冲区的数量。
By default, Flink takes 10% of the JVM heap size for network buffers, with a minimum of 64MB and a maximum of 1GB
默认情况下，Flink使用JVM堆大小的10%作为网络缓冲区，最小为64MB，最大为1GB。
You can adjust all these values via `taskmanager.network.memory.fraction`, `taskmanager.network.memory.min`, and
`taskmanager.network.memory.max`.

您可以通过以下方式调整所有这些值`taskmanager.network.memory.fraction`, `taskmanager.network.memory.min`, 和
`taskmanager.network.memory.max`.

Please refer to the [Configuration Reference]({{ site.docs-snapshot }}/ops/config.html#configuring-the-network-buffers) for details.
请参考[配置参考-配置网络缓冲区]({{ site.docs-snapshot }}/ops/config.html#configuring-the-network-buffers) 获取详细信息。
### My job fails with various exceptions from the HDFS/Hadoop code. What can I do?
### 从HDFS/Hadoop的代码遇到了各种异常，flinkjob失败了，我该怎么办?

The most common cause for that is that the Hadoop version in Flink's classpath is different than the
Hadoop version of the cluster you want to connect to (HDFS / YARN).

最常见的原因是Flink类路径中的Hadoop版本与您想要连接的集群(HDFS / YARN)的Hadoop版本不同。
The easiest way to fix that is to pick a Hadoop-free Flink version and simply export the Hadoop path and
classpath from the cluster.
修复这个问题的最简单方法是选择Hadoop-free Flink版本，并从集群中导出Hadoop路径和类路径。
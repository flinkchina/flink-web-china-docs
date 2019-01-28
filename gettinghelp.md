---
title: 获取帮助
---

<hr />

{% toc %}

## 有问题?

Apache Flink社区每天都会回答许多用户问题。您可以在档案中查找答案和建议，或者向社区寻求帮助和指导。
### 用户邮件列表
许多Flink用户、贡献者和提交者都订阅了Flink的用户邮件列表。用户邮件列表是寻求帮助的好地方。

在发布到邮件列表之前，您可以在邮件列表存档中搜索以下网站上讨论与您的邮件相关问题的电子邮件线程。
- [Apache Pony 邮件存档](https://lists.apache.org/list.html?user@flink.apache.org)
- [Nabble 存档](http://apache-flink-user-mailing-list-archive.2336050.n4.nabble.com/)

如果您想要邮寄到邮件列表中，您需要这样做  
1. 通过发送电子邮件订阅邮件列表到 `user-subscribe@flink.apache.org`,   
2. 回复确认邮件确认订阅  
3. 发送电子邮件至 `user@flink.apache.org`.  

请注意，如果您没有订阅，您将不会收到邮件的回复。

### Stack Overflow

Flink社区的许多成员都活跃在[Stack Overflow](https://stackoverflow.com)上。您可以使用[\[apache-flink\]](https://stackoverflow.com/questions/tagged/apache-flink)标签搜索问题和答案，或者发布您的问题。
## 发现bug?

如果您观察到可能由错误引起的意外行为，您可以搜索报告的错误或在[Flink的JIRA](https://issues.apache.org/jira/issues/?jql=project %3D FLINK)中提交错误报告

如果您不确定意外行为是否由错误引起，请向[用户邮件列表](#user-mailing-list)发送问题。
## 遇到错误信息？

识别错误消息的原因可能具有挑战性。下面，我们将列出最常见的错误消息并解释如何处理它们。
### 我有一个NotSerializableException

Flink使用Java序列化将应用程序逻辑(您实现的函数和操作，以及程序配置等)的副本分发到并行工作进程。因此，传递给API的所有函数都必须是可序列化的，正如[java.io.Serializable](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)所定义的那样  
如果您的函数是一个匿名内部类，请考虑以下情况:  
  - 使函数成为独立类或静态内部类
  - 使用Java 8 lambda函数

如果函数已经是静态类，则在创建该类的实例时检查所分配的字段。其中一个字段很可能包含不可序列化类型。
  - 在Java中，使用`RichFunction`并在`open()`方法中初始化有问题的字段。
  - 在Scala中，您通常可以简单地使用`lazy val`来延迟初始化，直到分布式执行发生。这可能会带来较小的性能成本。当然，你也可以在Scala中使用`RichFunction`。

### 使用Scala API，我得到一个关于隐式值和证据参数的错误。

此错误意味着无法提供类型信息的隐式值。确保您的代码中有一个`import org.apache.flink.streaming.api.scala._`(DataStream API)或`import org.apache.flink.api.scala._`(DataSet API)语句。

如果在接受泛型参数的函数或类中使用Flink操作，则必须为该参数提供类型信息。这可以通过使用上下文绑定来实现:  

~~~scala
def myFunction[T: TypeInformation](input: DataSet[T]): DataSet[Seq[T]] = {
  input.reduceGroup( i => i.toSeq )
}
~~~

有关Flink如何处理类型的深入讨论，请参见[类型提取和序列化]({{ site.docs-snapshot }}/dev/types_serialization.html) 。
### 我看到一个ClassCastException: X不能被强制转换为X。

当您看到样式`com.foo.X`中的异常不能转换为`com.foo.X`(或不能分配给`com.foo.X`)时，这意味着类`com.foo.X`的多个版本已被不同的类加载器加载，并且该类的类型试图彼此分配。  
原因可能是:
  - 通过`child-first` 类加载实现类复制。这是一种允许用户使用Flink使用的相同依赖项的不同版本的机制。但是，如果这些类的不同副本在Flink的核心和用户应用程序代码之间移动，就会出现这样的异常。要验证这就是原因，请尝试设置`classloader.resolve-order: parent-first` 。如果这使错误消失，请写信到邮件列表检查是否可能是一个错误。
  - 尽量不要使用interner，或者减少interner/cache的作用域，以确保在启动新任务执行时创建新缓存。 

### 我有AbstractMethodError或NoSuchFieldError。

这类错误通常表示在某些依赖项版本中出现了错误。这意味着在执行过程中加载的依赖项(库)版本与编译代码时加载的版本不同。

从Flink 1.4.0开始，与Flink的核心使用的依赖项或类路径中的其他依赖项(例如Hadoop)相比，应用程序JAR文件中的依赖项可能具有不同的版本。这要求激活`child-first`类加载，这是默认的。

If you see these problems in Flink 1.4+, one of the following may be true:
如果您在Flink 1.4+中看到这些问题，那么以下情况之一可能是正确的:
  - 您的应用程序代码中存在依赖项版本冲突。确保所有依赖项版本都是一致的。
  - 您与Flink无法通过`child-first`类加载来支持的库存在冲突。目前这些是Scala标准库类，以及Flink自己的类、日志api和任何Hadoop核心类。

### 我的DataStream应用程序不会产生任何输出，即使事件正在进入。
如果您的DataStream应用程序使用*事件时间*，请检查您的水印是否得到更新。如果没有生成水印，事件时间窗口可能永远不会触发，应用程序也不会产生结果。  
您可以在Flink的web UI(水印部分)中检查水印是否正在取得进展。  
### 我看到一个异常报告"Insufficient number of network buffers"(网络缓冲区数量不足)。
如果您以非常高的并行度运行Flink，可能需要增加网络缓冲区的数量。

默认情况下，Flink使用JVM堆大小的10%作为网络缓冲区，最小为64MB，最大为1GB。

您可以通过以下方式调整所有这些值`taskmanager.network.memory.fraction`, `taskmanager.network.memory.min`, 和
`taskmanager.network.memory.max`.

请参考[配置参考-配置网络缓冲区]({{ site.docs-snapshot }}/ops/config.html#configuring-the-network-buffers) 获取详细信息。

### 从HDFS/Hadoop的代码遇到了各种异常，flinkjob失败了，我该怎么办?

最常见的原因是Flink类路径中的Hadoop版本与您想要连接的集群(HDFS / YARN)的Hadoop版本不同。修复这个问题的最简单方法是选择Hadoop-free Flink版本，并从集群中导出Hadoop路径和类路径。
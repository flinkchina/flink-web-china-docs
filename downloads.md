---
title: "下载"
---

<hr />

<script type="text/javascript">
$( document ).ready(function() {
  // Handler for .ready() called.
  $('.ga-track').click( function () {
    console.log("tracking " + $(this).attr('id'))
    // we just use the element id for tracking with google analytics
    ga('send', 'event', 'button', 'click', $(this).attr('id'));
  });

});
</script>

{% toc %}

## 最新稳定版本 (v{{ site.FLINK_VERSION_STABLE }})

Apache Flink® {{ site.FLINK_VERSION_STABLE }} 是我们最新的稳定版本。


ApacheHadoop安装[非必须]（faq.html#how-does-flink-relate-to-the-hadoop-stack）来使用ApacheFlink。  
对于不使用任何Hadoop组件的用户，我们建议不使用捆绑的Hadoop库。


如果您计划将Apache Flink与Apache Hadoop一起使用(在YARN上运行Flink，连接到HDFS，连接到HBase，或者使用一些基于Hadoop的文件系统连接器)，那么选择捆绑匹配Hadoop版本的下载，或者使用Hadoop免费版本和[导出HADOOP_CLASSPATH环境变量](https://ci.apache.org/projects/flink/flink-docs-stable/ops/deployment/hadoop.html)。

### 二进制

<table class="table table-striped">
<thead>
    <tr>
    <th></th> <th>Scala 2.11</th> <th>Scala 2.12</th>
    </tr>
</thead>
<tbody>
    {% for binary_release in site.stable_releases %}
    <tr>
    <th>{{ binary_release.name }}</th>
    {% if binary_release.scala_211 %}
    <td><a href="{{ binary_release.scala_211.url }}" class="ga-track" id="{{ binary_release.scala_211.id }}">下载</a> (<a href="{{ binary_release.scala_211.asc_url }}">asc</a>, <a href="{{ binary_release.scala_211.sha512_url }}">sha512</a>)</td>
    {% else %}
    <td>Not supported.</td>
    {% endif %}

    {% if binary_release.scala_212 %}
    <td><a href="{{ binary_release.scala_212.url }}" class="ga-track" id="{{ binary_release.scala_212.id }}">Download</a> (<a href="{{ binary_release.scala_212.asc_url }}">asc</a>, <a href="{{ binary_release.scala_212.sha512_url }}">sha512</a>)</td>
    {% else %}
    <td>Not supported.</td>
    {% endif %}
    </tr>
    {% endfor %}
</tbody>
</table>

### 源码
<p>使用这些包之一，检查源代码或自己构建Flink:</p>

{% for source_release in site.source_releases %}
<div class="list-group">
  <!-- Source -->
  <a href="{{ source_release.url }}" class="list-group-item ga-track" id="{{ source_release.id }}">
    <!-- overrride margin/padding as the boxes otherwise overlap in subtle ways -->
    <h4 style="margin-top: 0px; padding-top: 0px;"><span class="glyphicon glyphicon-download" aria-hidden="true"></span> <strong>{{ source_release.name }}</strong> Source Release</h4>
  </a>
   (<a href="{{ source_release.asc_url }}">asc</a>, <a href="{{ source_release.sha512_url }}">sha512</a>)
</div>
{% endfor %}

### 可选组件

{% assign categories = site.optional_components | group_by: 'category' | sort: 'name' %}
{% for category in categories %}

<button class="collapsible" data-toggle="collapse" data-target="#{{category.name | slugify}}" aria-hidden="true">{{category.name}}<span class="glyphicon glyphicon-plus" style="float: right; font-size: 20px;"></span></button>
<div id="{{category.name | slugify}}" class="collapse">

{% assign components = category.items | | sort: 'name' %}
{% for component in components %}

<table class="table table-striped">
  <thead>
    <tr>
      <th><strong>{{ component.name }}</strong></th>
      {% if component.scala_dependent %}
      <th>Scala 2.11</th>
      <th>Scala 2.12</th>
      {% else %}
      <th></th>
      {% endif %}
    </tr>
  </thead>
  <tbody>
    {% for version in component.versions %}
      <tr>
        {% if component.scala_dependent %}
          <td>{{ version.version }}</td>
          {% if version.scala_211 %}
            <td><a href="{{ version.scala_211.url }}" class="ga-track" id="{{ version.scala_211.id }}">下载</a> (<a href="{{ version.scala_211.asc_url }}">asc</a>, <a href="{{ version.scala_211.sha512_url }}">sha1</a>)</td>
          {% else %}
            <td>Not supported.</td>
          {% endif %}
          {% if version.scala_212 %}
            <td><a href="{{ version.scala_212.url }}" class="ga-track" id="{{ version.scala_212.id }}">下载</a> (<a href="{{ version.scala_212.asc_url }}">asc</a>, <a href="{{ version.scala_212.sha512_url }}">sha1</a>)</td>
          {% else %}
            <td>Not supported.</td>
          {% endif %}
        {% else %}
          <td>{{ version.version }}</td>
          <td><a href="{{ version.url }}" class="ga-track" id="{{ version.id }}">下载</a> (<a href="{{ version.asc_url }}">asc</a>, <a href="{{ version.sha_url }}">sha1</a>)</td>
        {% endif %}
      </tr>
    {% endfor %}
  </tbody>
</table>

{% endfor %}
</div>
{% endfor %}

## 发布通知

Please have a look at the [Release Notes for Flink {{ site.FLINK_VERSION_STABLE_SHORT }}]({{ site.DOCS_BASE_URL }}flink-docs-release-{{ site.FLINK_VERSION_STABLE_SHORT }}/release-notes/flink-{{ site.FLINK_VERSION_STABLE_SHORT }}.html) if you plan to upgrade your Flink setup from a previous version.

## Verifying Hashes and Signatures

Along with our releases, we also provide sha512 hashes in `*.sha512` files and cryptographic signatures in `*.asc` files. The Apache Software Foundation has an extensive [tutorial to verify hashes and signatures](http://www.apache.org/info/verification.html) which you can follow by using any of these release-signing [KEYS](https://www.apache.org/dist/flink/KEYS).

## Maven依赖

You can add the following dependencies to your `pom.xml` to include Apache Flink in your project. These dependencies include a local execution environment and thus support local testing.
您可以将以下依赖项添加到项目中的`pom`文件中来引入Apache Flink。这些依赖项包括本地执行环境，因此支持本地测试。

- **Scala API**: To use the Scala API, replace the `flink-java` artifact id with `flink-scala_2.11` and `flink-streaming-java_2.11` with `flink-streaming-scala_2.11`.

- **Scala API**: 使用Scala API, 将 `flink-java` 和`flink-streaming-java_2.11`artifact id替换成`flink-scala_2.11` 和 `flink-streaming-scala_2.11`。

```xml
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-java</artifactId>
  <version>{{ site.FLINK_VERSION_STABLE }}</version>
</dependency>
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-streaming-java_2.11</artifactId>
  <version>{{ site.FLINK_VERSION_STABLE }}</version>
</dependency>
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-clients_2.11</artifactId>
  <version>{{ site.FLINK_VERSION_STABLE }}</version>
</dependency>
```

## 更新旧版本的策略

截至2017年3月，Flink社区[决定](http://apache-flink-mailing-list-archive.1008284.n3.nabble.com/DISCUSS-Time-based-releases-in-Flink-tp15386p15394.html) 支持当前和以前的小版本，并修复了一些bug。如果1.2.x是当前版本1.1.y是上一个受支持的小版本。两个版本都将收到关键问题的bug修复。

请注意，社区总是对讨论更旧版本的bug修复版本持开放态度。请通过dev@flink.apache.org邮件列表与开发人员联系。

## 所有的稳定版本

All Flink releases are available via [https://archive.apache.org/dist/flink/](https://archive.apache.org/dist/flink/) including checksums and cryptographic signatures. At the time of writing, this includes the following versions:

### Flink

- Flink 1.7.1 - 2018-12-21 ([Source](https://archive.apache.org/dist/flink/flink-1.7.1/flink-1.7.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.7.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/api/scala/index.html))
- Flink 1.7.0 - 2018-11-30 ([Source](https://archive.apache.org/dist/flink/flink-1.7.0/flink-1.7.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.7.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.7/api/scala/index.html))
- Flink 1.6.3 - 2018-12-22 ([Source](https://archive.apache.org/dist/flink/flink-1.6.3/flink-1.6.3-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.6.3/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/scala/index.html))
- Flink 1.6.2 - 2018-10-29 ([Source](https://archive.apache.org/dist/flink/flink-1.6.2/flink-1.6.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.6.2/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/scala/index.html))
- Flink 1.6.1 - 2018-09-19 ([Source](https://archive.apache.org/dist/flink/flink-1.6.1/flink-1.6.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.6.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/scala/index.html))
- Flink 1.6.0 - 2018-08-08 ([Source](https://archive.apache.org/dist/flink/flink-1.6.0/flink-1.6.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.6.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.6/api/scala/index.html))
- Flink 1.5.6 - 2018-12-21 ([Source](https://archive.apache.org/dist/flink/flink-1.5.6/flink-1.5.6-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.6/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.5 - 2018-10-29 ([Source](https://archive.apache.org/dist/flink/flink-1.5.5/flink-1.5.5-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.5/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.4 - 2018-09-19 ([Source](https://archive.apache.org/dist/flink/flink-1.5.4/flink-1.5.4-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.4/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.3 - 2018-08-21 ([Source](https://archive.apache.org/dist/flink/flink-1.5.3/flink-1.5.3-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.3/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.2 - 2018-07-31 ([Source](https://archive.apache.org/dist/flink/flink-1.5.2/flink-1.5.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.2/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.1 - 2018-07-12 ([Source](https://archive.apache.org/dist/flink/flink-1.5.1/flink-1.5.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.5.0 - 2018-05-25 ([Source](https://archive.apache.org/dist/flink/flink-1.5.0/flink-1.5.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.5.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.5/api/scala/index.html))
- Flink 1.4.2 - 2018-03-08 ([Source](https://archive.apache.org/dist/flink/flink-1.4.2/flink-1.4.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.4.2/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/scala/index.html))
- Flink 1.4.1 - 2018-02-15 ([Source](https://archive.apache.org/dist/flink/flink-1.4.1/flink-1.4.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.4.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/scala/index.html))
- Flink 1.4.0 - 2017-11-29 ([Source](https://archive.apache.org/dist/flink/flink-1.4.0/flink-1.4.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.4.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.4/api/scala/index.html))
- Flink 1.3.3 - 2018-03-15 ([Source](https://archive.apache.org/dist/flink/flink-1.3.3/flink-1.3.3-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.3.3/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/scala/index.html))
- Flink 1.3.2 - 2017-08-05 ([Source](https://archive.apache.org/dist/flink/flink-1.3.2/flink-1.3.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.3.2/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/scala/index.html))
- Flink 1.3.1 - 2017-06-23 ([Source](https://archive.apache.org/dist/flink/flink-1.3.1/flink-1.3.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.3.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/scala/index.html))
- Flink 1.3.0 - 2017-06-01 ([Source](https://archive.apache.org/dist/flink/flink-1.3.0/flink-1.3.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.3.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.3/api/scala/index.html))
- Flink 1.2.1 - 2017-04-26 ([Source](https://archive.apache.org/dist/flink/flink-1.2.1/flink-1.2.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.2.1/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/api/scala/index.html))
- Flink 1.2.0 - 2017-02-06 ([Source](https://archive.apache.org/dist/flink/flink-1.2.0/flink-1.2.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.2.0/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.2/api/scala/index.html))
- Flink 1.1.5 - 2017-03-22 ([Source](https://archive.apache.org/dist/flink/flink-1.1.5/flink-1.1.5-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.5/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/scala/index.html))
- Flink 1.1.4 - 2016-12-21 ([Source](https://archive.apache.org/dist/flink/flink-1.1.4/flink-1.1.4-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.4/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/scala/index.html))
- Flink 1.1.3 - 2016-10-13 ([Source](https://archive.apache.org/dist/flink/flink-1.1.3/flink-1.1.3-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.3/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.1/api/scala/index.html))
- Flink 1.1.2 - 2016-09-05 ([Source](https://archive.apache.org/dist/flink/flink-1.1.2/flink-1.1.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.2/))
- Flink 1.1.1 - 2016-08-11 ([Source](https://archive.apache.org/dist/flink/flink-1.1.1/flink-1.1.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.1/))
- Flink 1.1.0 - 2016-08-08 ([Source](https://archive.apache.org/dist/flink/flink-1.1.0/flink-1.1.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.1.0/))
- Flink 1.0.3 - 2016-05-12 ([Source](https://archive.apache.org/dist/flink/flink-1.0.3/flink-1.0.3-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.0.3/), [Docs]({{site.DOCS_BASE_URL}}flink-docs-release-1.0/), [Javadocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.0/api/java), [ScalaDocs]({{site.DOCS_BASE_URL}}flink-docs-release-1.0/api/scala/index.html))
- Flink 1.0.2 - 2016-04-23 ([Source](https://archive.apache.org/dist/flink/flink-1.0.2/flink-1.0.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.0.2/))
- Flink 1.0.1 - 2016-04-06 ([Source](https://archive.apache.org/dist/flink/flink-1.0.1/flink-1.0.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.0.1/))
- Flink 1.0.0 - 2016-03-08 ([Source](https://archive.apache.org/dist/flink/flink-1.0.0/flink-1.0.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-1.0.0/))
- Flink 0.10.2 - 2016-02-11 ([Source](https://archive.apache.org/dist/flink/flink-0.10.2/flink-0.10.2-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.10.2/))
- Flink 0.10.1 - 2015-11-27 ([Source](https://archive.apache.org/dist/flink/flink-0.10.1/flink-0.10.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.10.1/))
- Flink 0.10.0 - 2015-11-16 ([Source](https://archive.apache.org/dist/flink/flink-0.10.0/flink-0.10.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.10.0/))
- Flink 0.9.1 - 2015-09-01 ([Source](https://archive.apache.org/dist/flink/flink-0.9.1/flink-0.9.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.9.1/))
- Flink 0.9.0 - 2015-06-24 ([Source](https://archive.apache.org/dist/flink/flink-0.9.0/flink-0.9.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.9.0/))
- Flink 0.9.0-milestone-1 - 2015-04-13 ([Source](https://archive.apache.org/dist/flink/flink-0.9.0-milestone-1/flink-0.9.0-milestone-1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.9.0-milestone-1/))
- Flink 0.8.1 - 2015-02-20 ([Source](https://archive.apache.org/dist/flink/flink-0.8.1/flink-0.8.1-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.8.1/))
- Flink 0.8.0 - 2015-01-22 ([Source](https://archive.apache.org/dist/flink/flink-0.8.0/flink-0.8.0-src.tgz), [Binaries](https://archive.apache.org/dist/flink/flink-0.8.0/))
- Flink 0.7.0-incubating - 2014-11-04 ([Source](https://archive.apache.org/dist/incubator/flink/flink-0.7.0-incubating/flink-0.7.0-incubating-src.tgz), [Binaries](https://archive.apache.org/dist/incubator/flink/flink-0.7.0-incubating/))
- Flink 0.6.1-incubating - 2014-09-26 ([Source](https://archive.apache.org/dist/incubator/flink/flink-0.6.1-incubating/flink-0.6.1-incubating-src.tgz), [Binaries](https://archive.apache.org/dist/incubator/flink/flink-0.6.1-incubating/))
- Flink 0.6-incubating - 2014-08-26 ([Source](https://archive.apache.org/dist/incubator/flink/flink-0.6-incubating-src.tgz), [Binaries](https://archive.apache.org/dist/incubator/flink/))

### Flink-shaded
- Flink-shaded 5.0 - 2018-10-15 ([Source](https://archive.apache.org/dist/flink/flink-shaded-5.0/flink-shaded-5.0-src.tgz))
- Flink-shaded 4.0 - 2018-06-06 ([Source](https://archive.apache.org/dist/flink/flink-shaded-4.0/flink-shaded-4.0-src.tgz))
- Flink-shaded 3.0 - 2018-02-28 ([Source](https://archive.apache.org/dist/flink/flink-shaded-3.0/flink-shaded-3.0-src.tgz))
- Flink-shaded 2.0 - 2017-10-30 ([Source](https://archive.apache.org/dist/flink/flink-shaded-2.0/flink-shaded-2.0-src.tgz))
- Flink-shaded 1.0 - 2017-07-27 ([Source](https://archive.apache.org/dist/flink/flink-shaded-1.0/flink-shaded-1.0-src.tgz))

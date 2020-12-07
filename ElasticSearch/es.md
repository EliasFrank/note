### Lucecne

是一套信息检索工具包，jar包，不包含搜索引擎系统

包含：索引结构，读写索引的工具，排序，搜索规则，工具类。。。

### ElasticSearch简介

es是一个实时分布式搜索和分析引擎，他让你以前所未有的速度处理大数据成为可能

它用于**全文搜索**、**结构化搜索**、**分析**以及将这三者混合使用

### Solr简介

Solr是Apache下的一个顶级开源项目，采用java开发，他是基于Lucene的全文搜索服务器。Solr可独立运行，可以运行在Jetty、Tomcat等。Solr索引的实现方法很简单，用POST方法向Solr服务器发送一个描述Field及其内容的XML文档，Slor根据xml文档添加、删除、更新索引。Solr搜索只需要发送HTPP GET请求，然后对Solr返回xml、json等格式的查询结果进行解析，组织页面布局。Solr搜索只需要发送HTTP GET请求，然后对Solr返回XML、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况

### ElasticSearch和Solr总结

1. es基本是开箱即用 ，非常简单，Solr安装略微复杂一丢丢
2. Solr利用Zookeeper进行分布式管理，而ElasticSearch自身带有分布式协调管理功能
3. Solr支持更多格式的数据，比如JSON\XML\CSV，而es仅支持json文件格式
4. Solr官方提供的功能更多，而es本身更注重于核心功能，高级功能多由第三方插件提供，例如图形化界面需要kibana友好支撑
5. Solr查询快，但更新索引时慢（即插入删除慢），用于电商等查询多的应用
    * ES建立索引快（查询慢），即实时查询快，用facebook新浪等搜索
    * Solr是传统搜索应用的有力解决方案，但ES更适用于新兴的实时搜索应用
6. Solr比较成熟，有一个更大、更成熟的用户、开发和贡献者社区，而ES相对开发维护者较少，更新较快，学习使用成本高

### SlasticSearch安装

####  window下安装

解压就可以使用

![1606394597635](H:\Typora\pics\1606394597635.png)

目录

```
bin 启动文件
config 配置文件
	log4j2 日志配置文件
	jvm.options java虚拟机相关的配置
	elasticsearch.yml elasticsearch的配置文件，默认端口9200  跨域
lib 相关jar包
logs 日志
modules 功能模块
plugins 插件 ik
```

ElasticSearch Head安装谷歌插件

配置es

```yml
http:
  cors:
    enabled : true
    allow-origin : "*"
```

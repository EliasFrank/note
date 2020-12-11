ik分词器插件

分词：即把一段中文或者别的划分成一个个的关键字，我们再搜索时候会把自己的信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一个匹配操作，默认的中文分词器是将每个字看成一个词，比如：“就这水平”会被分成“就”“这”“水”“平”。这显然是不符合要求的，所以我们需要安装中文分词器ik来解决这个问题

ik提供了两个分词算法：ik_smart和ik_max_word，其中ik_smart为最少切分，ik_max_word为最细粒度划分。

ik_smart为最少切分

<img src="upload\image-20201208131747560.png" alt="image-20201208131747560" style="zoom:33%;" />

ik_max_word为最细粒度划分，穷尽词库的可能

<img src="upload\image-20201208131814853.png" alt="image-20201208131814853" style="zoom: 33%;" />

发现问题：“傻逼”被拆开了

这种自己需要的词，需要我们自己加到我们的分词器的字典里

在分词器的config目录下，添加一个*.dic文件(文件编码需要为UTF-8)，在里面每一行写上一个词，然后在IKAnalyzer.cfg.xml里面添加这个文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict">hl.dic</entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

重启es

```shell
[INFO ][o.w.a.d.Monitor          ] [FAKE-ANDROID] [Dict Loading] H:\elasticsearch\elasticsearch-7.6.0\plugins\ik\config\hl.dic
```


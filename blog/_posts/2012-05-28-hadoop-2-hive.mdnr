---
layout: default
title: hadoop与hive的映射
---

 {{ page.title }}
================
<p class="meta">28 May 2012 - GuangZhou</p>

hadoop代码转向hive代码说到，很多MR任务可以由hive完成。这几天，作了一些简单的汇总：

文件切割（多输入多输出）
-----------------------
需求：数据LOG包含多种信息，需要将不同的数据信息重定向到不同的文件。  
hadoop：MultipleInputs、multipleoutputs两个类主要负责多输入多输出的处理。  
hive：利用union all和Custom map/reduce scripts，可以对不同的输入进行不同的处理，然后合并结果；Multi Table/File Inserts、Dynamic-partition Insert这两个主要是对同个数据源（或者某个中间结果），根据条件重定向输出，特别是中间结果，可以减少很多MR Job。  

获取文件的输入路径
-----------------------
需求：数据LOG可能包含日志服务器和日期信息，每个文件包含的是不同服务器、不同日期的数据。这个时候只能通过文件名区分数据的服务器、日期。  
hadoop： ((FileSplit) reporter.getInputSplit()).getPath()  
hive：hive建表时已经指定数据存储路径，只能通过元数据库获取。但是直到0.8.0版本之前，hive都一直没有提供相应的函数和接口。hive0.8.0提供了INPUT__FILE__NAME、BLOCK__OFFSET__INSIDE__FILE两个虚拟列，可以获取文件名和文件位置。我非常喜欢hive这些改进。hadoop之所以能够广泛使用，是因为提供的底层接口，让用户自定义处理；hive虽然提高了编写脚本的效率，但是功能毕竟有限。这就类似一些高级语言也会提供一些底层语言的语法，以适合不同的场景应用。  

两表数据join
-----------------------
需求：数据信息不可能只保存在一个数据文件/数据中，实际工作经常需要关联多张表，才能完整获取文件信息。  
hadoop： 分为map side join 和reduce side join两种。reduce side join的实现方式是在map阶段给数据源打标签，区分数据文件；然后在reduce阶段，获取来自多个文件的相同key的value， 然后对于同key，进行多个文件间的数据join。map side join是一种改进。如果关联的两个表，一个表非常大，而另一个表可以直接存放到内存中。这样，可以将可以放到内存的表直接复制到每个map，然后只需要扫描大表，关联数据即可。  
hive：LEFT、RIGHT、FULL JOIN是常见的两表数据关联。而判断表A中的KEY是否在表B中，则需要用到LEFT SEMI JOIN。  

获取配置文件
-----------------------
需求：代码与配置文件是分离的，这样有变动的时候，只需要修改配置文件就能获得良好的扩展。  
hadoop：DistributedCache 是Map/Reduce框架提供的功能，能够缓存应用程序所需的文件 （包括文本，档案文件，jar文件等）。DistributedCache.addCacheFile是相应实现。  
hive：add files  jars archives。  

不同的数据不同的处理
-----------------------
hadoop：不同的输入文件可由不同的map函数处理MultipleInputs.addInputPath(JobConf conf, InputPath, TextInputFormat.class, Mapper.class) 。  
hive：SELECT TRANSFROM（输入字段) USING '处理脚本'  AS（输出字段） WHERE 根据条件筛选相应的数据   UNION ALL（归并不同处理脚本输出的数据）。  

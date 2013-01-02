---
layout: default
title: Hive编码规范
---

 {{ page.title }}
================
<p class="meta">28 Aug 2012 - GuangZhou</p>

1、关键字（内置函数最好大写，便于udf区分；我们之前历史原因，没有大写），如，
     `SELECT TRANSFORM(urs, dt)
     USING 'python consume_loss_mall.py'
     AS (urs string, calcDate string)
     FROM src_wh_buyitem_day
     WHERE dt >= ${begindt} AND dt <= ${enddt}`
2、SELECT/FROM/WHERE/JOIN/ORDER BY/GROUP BY/LIMIT等子句最好另其一行写，如上所示  
3、＝、<=、>=等前后加上一个空格  
4、字符常量使用单引号,避免使用双引号  
5、自定义函数小写  
6、复杂的HQL语句，用--加上注释  
7、多表连接时，使用表的别名来引用列（而且通过聚合函数生成的列必须要有别名，才能引用）  
8、多利用中间表，可以减少部分job重复运行  
9、多利用自定义流，可以实现复杂的计算，减少编码量。（当然资源量会增加，但是相对于游戏数据量，以及job启动的时间，目前看还是值得的，当然未来是未知的=v=）  
10、explain是好东西，虽然我现在比较少用  
11、在SQL更多是 insert  ... select ... FROM 写法，但HQL推荐是FROM (SELECT ...FROM...) INSERT...写法。这个其实是总结中间表的使用心得来的。而且FROM (SELECT ...FROM...) 可以理解为map操作，实现复杂的一些中间层实现；而INSERT 等操作可以理解为reduce操作，对中间层实现结果汇总  


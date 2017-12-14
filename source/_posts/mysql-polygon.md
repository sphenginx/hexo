title: MySQL 判断点是否在指定多边形区域内
date: 2017-12-13 20:03:01
categories: 日薪越亿
tags: [MySQL, polygon]
---

近期接了个 `网格区域管理` 的需求， 具体实现为: 首先建立几个百度地图的坐标点， 其次可以在百度地图上画一个多边形，把需要的坐标点圈在多边形里面。  本文将介绍使用mysql判断点是否在指定多边形区域内的方法，提供完整流程。

## 1、创建测试表

```sql
CREATE TABLE `zone` (
 `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
 `polygongeo` text NOT NULL,
 PRIMARY KEY (`id`)
) ENGINE=MYISAM DEFAULT CHARSET=utf8;
```

> 注意：空间索引只能在存储引擎为MYISAM的表中创建（MySQL5.7版本以上可以用Innodb引擎）

## 2.插入多边形数据

```sql
insert into zone(polygongeo) values('POLYGON((1 1,1 5,5 5,5 1,1 1))');
```

> 注意：`polygongeo`包含的点必须是个闭环（第一个点 和 最后一个点必须一致） 

## 3.判断点是否在多边形区域

### 测试 POINT(3, 4)

> `where` 查询可以用 `MBRWithin` 或者 `MBRCONTAINS`(参数位置交换)

```sql
select AsText(polygongeo) from zone where MBRWithin(POLYGONFROMTEXT('POINT(3 4)'), GEOMFROMTEXT(polygongeo));
```

输出: POLYGON((1 1,1 5,5 5,5 1,1 1)) 
表示点 POINT(3, 4) 在多边形区域内 

### 测试 POINT(6, 1)

```sql
select AsText(polygongeo) from zone where MBRWithin(POLYGONFROMTEXT('POINT(6 1)'),  GEOMFROMTEXT(polygongeo));
```

输出: 空 
表示点 POINT(6, 1) 在多边形区域外 

## 4、参考资料
+ 0. [mysql 判断点是否在指定多边形区域内](http://blog.csdn.net/fdipzone/article/details/53896842)  
+ 1. [mongodb 判断坐标是否在指定多边形区域内的方法](http://www.voidcn.com/article/p-wpvigmsq-bkg.html)  
+ 2. [怎么判断一个点是否在多边形区域内](http://www.voidcn.com/article/p-agdcdenf-da.html)

总结：mysql空间查询并不很适合地图坐标，因此查询地图坐标可以使用mongodb实现，参考：[《mongodb 判断坐标是否在指定多边形区域内的方法》](http://blog.csdn.net/fdipzone/article/details/52374630)
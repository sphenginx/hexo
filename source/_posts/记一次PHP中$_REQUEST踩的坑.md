title: 记一次PHP中$_REQUEST踩的坑
date: 2014-11-27 09:27:40
categories: 日薪越亿
tags: [request, php]
---
__前记__

今天测试返回一个API调用接口不对的bug，查看API发现变量是通过$_REQUEST来获取参数的，难道获取方法有问题？

__排查__

既然有了怀疑，那么我们来打印下变量吧，通过实验发现：
 **$_REQUEST获取的参数 和URL中的不一致**，
为啥会不一致呢，请教了下Google大神，查看`ini`的设置，发现
```var
$ variables_order = "EGPCS";
```
打印EGPCS时发现cookie 和 get中有相同的变量，原来是cookie中的参数覆盖了get中的参数，囧~ 

> （腹诽：cookie中应该加个前缀啥的吧……）：

__结论__

php版本< 5.3时 会 根据variables_order的设置读取参数；

PHP版本 > 5.3时 会根据request_order的设置读取参数；

PHP version < 5.3时默认读取顺序为：**E G P C S**，即 ：
```order
$ variables_order = "EGPCS";
```
variables_order 系统在定义PHP预定义变量，EGPCS 是 `Environment, Get, Post, Cookie, and Server` 的简写。 

这个变量主要是在php执行时，对超级变量创建的设置。EGPCS对应的超级变量为`$_ENV`, `$_GET`, `$_POST`, `$_COOKIE`, `$_SERVER`。如果variables_order被设置为"",则对应的超级全局变量的值都为空数组。

所以，要想`$_REQUEST`包含的预定义变量的值，variables_order必须有对应的设置。例如，variables_order = "G"，则$_REQUEST中肯定不会有post的值。

在`register_globals=on`的情况，这个配置的顺序将影响对应变量的值，重复key，后边的会覆盖前边的。
```order
$ request_order = "PGC"
```
这个变量，说明`$_REQUEST`包含哪些类型的外部数据、数据加载的顺序。这个是有顺序的，如果key重复，后边的就会覆盖前边的值。

>	比如:
`$_GET`里面有个 `$_GET['id']=2` ,  `$_POST` 里有一个 `$_POST['id'] =3`。
如果request_order = "PG"的形式设置，那么 `$_REQUEST['id']=2`。
如果request_order = "GP"的形式设置，则 `$_REQUEST['id']=3`。
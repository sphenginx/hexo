title: jQuery中each如何跳出循环并终止后续代码执行？
date: 2014-12-24 17:02:50
categories: 日新月异
tags: jquery
---
__前记__

今天在表单提交验证的时候，需要检测商品是否有规格，因为商品有多个规格，所以用到了jquery的each，循环读取每个规格，如果值为空，则return false；但是，后来发现return false 表单还是提交了，百思不得其解。

__排查__

后来查看manual才发现：`jquery的each函数中，return false只能提前终止循环，相当于break；return true相当于 continue。`

__解决__

那么，如何才能阻止表单的提交呢，想了N久，想到在each循环前，先定义一个变量为true，然后在循环读取规格的时候，如果值为空，则把变量定义为false，同时return false，代码如下：
```jquery
#出现bug的代码
$(".spec").each(function(){
    var s =$(this).val();
    if(!s){
        alert('规格不能为空');
        return false;
    }
})
```
这样return false，只是终止循环，并没有终止程序。

```jquery
#更新后的代码
var real_true = true;
$(".spec").each(function(){
    var s =$(this).val();
    if(!s){
	 alert('规格不能为空');
	  real_true = false;
         return false;
    }
})
if(!real_true){
    return false;
}
```
暂时想到了这个方法，如果大家有更好的方法，欢迎一起探讨~
title: Markdown学习笔记
date: 2015-12-07 17:03:01
categories: 日薪越亿
tags: markdown
---

## Markdown概述

### 宗旨
Markdown 是一种轻量级标记语言，创始人为约翰·格鲁伯（John Gruber）。它允许人们 `使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML(或者HTML)文档`。

可读性，无论如何，都是最重要的。一份使用 Markdown 格式撰写的文件应该可以直接以纯文本发布，并且看起来不会像是由许多标签或是格式指令所构成。Markdown 语法受到一些既有 text-to-HTML 格式的影响，包括 _Setext_、_atx_、_Textile_、_reStructuredText_、_Grutatext_ 和 _EtText_，而最大灵感来源其实是纯文本电子邮件的格式。

总之， Markdown 的语法全由一些符号所组成，这些符号经过精挑细选，其作用一目了然。比如：在文字两旁加上星号，看起来就像*强调*。Markdown 的列表看起来，嗯，就是列表。Markdown 的区块引用看起来就真的像是引用一段文字，就像你曾在电子邮件中见过的那样。

### 兼容HTML

Markdown 语法的目标是：成为一种适用于网络的书写语言。

Markdown 不是想要取代 HTML，甚至也没有要和它相近，它的语法种类很少，只对应 HTML 标记的一小部分。Markdown 的构想不是要使得 HTML 文档更容易书写。在我看来， HTML 已经很容易写了。Markdown 的理念是，能让文档更容易读、写和随意改。HTML 是一种发布的格式，Markdown 是一种书写的格式。就这样，Markdown 的格式语法只涵盖纯文本可以涵盖的范围。

不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写。不需要额外标注这是 HTML 或是 Markdown；只要直接加标签就可以了。

要制约的只有一些 HTML 区块元素――比如 `<div>`、`<table>`、`<pre>`、`<p>` 等标签，必须在前后加上空行与其它内容区隔开，还要求它们的开始标签与结尾标签不能用制表符或空格来缩进。Markdown 的生成器有足够智能，不会在 HTML 区块标签外加上不必要的 `<p>` 标签。

例子如下，在 Markdown 文件里加上一段 HTML 表格：
```
这是一个普通段落。

<table>
    <tr>
        <td>Foo</td>
    </tr>
</table>

这是另一个普通段落。
```

请注意，在 HTML 区块标签间的 Markdown 格式语法将不会被处理。比如，你在 HTML 区块内使用 Markdown 样式的*强调*会没有效果。

HTML 的区段（行内）标签如 `<span>`、`<cite>`、`<del>` 可以在 Markdown 的段落、列表或是标题里随意使用。依照个人习惯，甚至可以不用 Markdown 格式，而直接采用 HTML 标签来格式化。举例说明：如果比较喜欢 HTML 的 `<a>` 或 `<img>` 标签，可以直接使用这些标签，而不用 Markdown 提供的链接或是图像标签语法。

和处在 HTML 区块标签间不同，Markdown 语法在 HTML 区段标签间是有效的。

## Markdown基本语法

### 代码区域

如果你只想高亮语句中的某个函数名或关键字，可以使用 <font color="red">\`function_name()\`</font>  实现

通常编辑器根据代码片段适配合适的高亮方法，但你也可以用 \`\`\` 包裹一段代码，并指定一种语言

支持的语言：`actionscript`, `apache`, `bash`, `clojure`, `cmake`, `coffeescript`, `cpp`, `cs`, `css`, `d`, `delphi`, `django`, `erlang`, `go`, `haskell`, `html`, `http`, `ini`, `java`, `javascript`, `json`, `lisp`, `lua`, `matlab`, `nginx`, `objectivec`, `perl`, `php`, `python`, `r`, `ruby`, `scala`, `smalltalk`, `sql`, `tex`, `vbscript`, `xml`

\`\`\` javascript
$(document).ready(function () {
    alert('hello world');
});
\`\`\`

也可以使用 4 空格缩进，再贴上代码，实现相同的的效果
<pre>
    def g(x):
        yield from range(x, 0, -1)
    yield from range(x)
</pre>

### 标题
Markdown提供了两种方式（Setext和Atx）来显示标题。  
例:
```
Setext方式
标题1
=================
标题2
-----------------

Atx方式
# 标题1
## 标题2
###### 标题6
```
### 换行
在文字的末尾使用两个或两个以上的空格来表示换行。

### 引用
行首使用`[大于号+空格]`表示引用段落，内部可以嵌套多个引用。

语法：
```
> 这是一个引用，
> 这里木有换行，
> 在这里换行了。
> > 内部嵌套
```
效果：
> 这是一个引用，
> 这里木有换行，
> 在这里换行了。
> > 内部嵌套

### 列表
- 无序列表使用 *、 + 或 - 后面加上空格来表示。

语法：
```
* Item 1
* Item 2
* Item 3

+ Item 1
+ Item 2
+ Item 3

- Item 1
- Item 2
- Item 3
```
效果：
* Item 1
* Item 2
* Item 3

+ Item 1
+ Item 2
+ Item 3

- Item 1
- Item 2
- Item 3

- 有序列表使用数字加英文句号加空格表示。  
语法：  

```
1. 列表前使用 [数字+空格]
2. 我们会自动帮你添加数字
7. 不用担心数字不对，显示的时候我们会自动把这行的 7 纠正为 3
```

效果：  
1. 列表前使用 [数字+空格]
2. 我们会自动帮你添加数字
3. 不用担心数字不对，显示的时候我们会自动把这行的 7 纠正为 3

### 强调
Markdown使用 * 或 _ 表示强调。

语法：
```
单星号 = *斜体*
单下划线 = _斜体_
双星号 = **加粗**
双下划线 = __加粗__
删除线 = ~~加粗~~
```
效果：  
单星号 = *斜体*
单下划线 = _斜体_
双星号 = **加粗**
双下划线 = __加粗__
删除线 = ~~加粗~~

### 表格

语法：
```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

或

dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz
```
效果：  

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

或

dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz


### 链接
Markdown支持两种风格的链接：`Inline` 和 `Reference` 。

Inline ：以中括号标记显示的链接文本，后面紧跟用小括号包围的链接。如果链接有title属性，则在链接中使用空格加"title属性"。  
Reference ：一般应用于多个不同位置使用相同链接。通常分为两个部分，调用部分为[链接文本][ref]；定义部分可以出现在文本中的其他位置，格式为 [ref]: http://some/link/address (可选的标题)。  
注：ref中不区分大小写。  
语法：
```
这是一个Inline[示例](http://sphenginx.github.io "可选的title")。
这是一个Reference[示例][ref]。
[ref]:http://sphenginx.github.io (可选的title)
```

效果：
这是一个Inline[示例](http://sphenginx.github.io "可选的title")。  
这是一个Reference[示例][ref]。  
[ref]:http://sphenginx.github.io (可选的title)  

### 图片

图片的使用方法基本上和链接类似，只是在中括号前加叹号。

语法:  
```
![图片名称](http://图片网址)
![GitHub头像](https://avatars1.githubusercontent.com/u/1829395?v=3&s=460)
```

效果:  

![GitHub头像](https://avatars1.githubusercontent.com/u/1829395?v=3&s=460)

注：Markdown不能设置图片大小，如果必须设置则应使用HTML标记 `<img>`。

```
<img src="https://avatars1.githubusercontent.com/u/1829395?v=3&s=460" width="400" height="100">
```
效果如下：

<img src="https://avatars1.githubusercontent.com/u/1829395?v=3&s=460" width="400" height="100">

### 分隔线
在一行中使用三个或三个以上的*、-或_可以添加分隔线，其中可以有空白，但是不能有其他字符。

语法：

```
***
```

效果：

***


### 转义字符
Markdown中的转义字符为\，可以转义的有：
```
\\ 反斜杠
\` 反引号
\* 星号
\_ 下划线
\{\} 大括号
\[\] 中括号
\(\) 小括号
\# 井号
\+ 加号
\- 减号
\. 英文句号
\! 感叹号
```

### 颜色与字体
markdown 不支持颜色和字体，所以如果想添加颜色或字体，只能使用 html 标签来实现这些需求了。

语法:  
```
<font color="red">我是红色的</font>
```

效果:  
<font color="red">我是红色的</font>

## 高级技巧

### 制作一份待办事宜 Todo 列表
语法：  

```
- [ ] 支持以 PDF 格式导出文稿
- [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
- [x] 新增 Todo 列表功能
- [x] 修复 LaTex 公式渲染问题
- [x] 新增 LaTex 公式编号功能
```
效果：

- [ ] 支持以 PDF 格式导出文稿
- [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
- [x] 新增 Todo 列表功能
- [x] 修复 LaTex 公式渲染问题
- [x] 新增 LaTex 公式编号功能

### 高效绘制流程图|序列图

不知道为啥Git pages 不支持这种写法，可以copy在最新的Markdown语法解析器里面试试，或者点击 [Markdown在线编辑器](https://www.zybuluo.com/mdeditor) 尝试

```
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end

st->op->cond
cond(yes)->e
cond(no)->op
```


### 行内 HTML 元素

目前只支持部分段内 HTML 元素效果，包括 `<kdb>` `<b>` `<i>` `<em>` `<sup>` `<sub>` `<br>` ，如

- 键位显示
```
使用 <kbd>Ctrl<kbd>+<kbd>Alt<kbd>+<kbd>Del<kbd> 重启电脑
```
效果:  
使用  <kbd>Ctrl<kbd>+<kbd>Alt<kbd>+<kbd>Del<kbd>  重启电脑

- 代码块
```
使用 <pre></pre> 元素同样可以形成代码块
```

- 粗斜体
```
<b> Markdown 在此处同样适用，如 *加粗* </b>
```


### 扩展
支持 jsfiddle、gist、runjs、优酷视频，直接填写 url，在其之后会自动添加预览点击会展开相关内容。
```
http://{url_of_the_fiddle}/embedded/[{tabs}/[{style}]]/
https://gist.github.com/{gist_id}
http://runjs.cn/detail/{id}
http://v.youku.com/v_show/id_{video_id}.html
```
### 公式

当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现。提交后，问答和文章页会根据需要加载 Mathjax 对数学公式进行渲染。如：
```
$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a}. $$

$$
x \href{why-equal.html}{=} y^2 + 1
$$
```

效果如下：  
$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a}. $$

$$
x \href{why-equal.html}{=} y^2 + 1
$$

## Markdown编辑器

- [欢迎使用 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/mdeditor)

Win平台
- [MarkdownPad](http://markdownpad.com/)
- [Haroopad - The next document processor](http://pad.haroopress.com/)  
  进入首页之后，点击用户进入下载界面

Linux平台
- [ReText](http://sourceforge.net/p/retext/home/ReText/)

Mac平台
- [Mou](http://mouapp.com/)


## 结语
以上几种格式是比较常用的格式，所以我们针对这些语法做了比较详细的说明。除这些之外，Markdown 还有其他语法，如想了解和学习更多，可以参考这篇[『Markdown 语法说明』](http://wowubuntu.com/markdown/)。:smile: (It seems the hexo does not support emoji)…… 

## 附
- [Github markdown guide](https://guides.github.com/features/mastering-markdown/)
- [emoji-cheat-sheet](http://www.emoji-cheat-sheet.com/)

强烈建议您现在就立马用 Markdown 写一篇文章吧，体会一下 Markdown 的优雅之处！

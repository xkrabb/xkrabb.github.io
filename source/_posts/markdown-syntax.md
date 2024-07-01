title: markdown syntax
date: 2016-06-28 16:43:07
tag: MarkDown
---
#### MarkDown语法

----简单[易读易写]，且兼容HTML语法(混合写MD和HTML语法)，span,cite,del可以在 Markdown 的段落、列表或是标题里随意使用。

----对于特殊字符 *<* 和 *&* ,需要用< 和& 转义，就连在超链接中也需要。

****

<!-- more -->

####  区块元素：


##### 段落和换行：

段落是由一个或多个连续的文本行组成，它的前后要有一个以上的空行。段落中markdown会自动换行，如果需要依赖 Markdown 强制换行的话，在插入处先按入两个以上的空格然后回车。

##### 标题：

Markdown 支持两种标题的语法，类 Setext 和类 atx 形式。
* 类 Setext 形式是用底线的形式，利用 = （最高阶标题）和 - （第二阶标题）：

```
This is an H1
=============
This is an H2
-------------
```

* 类 Atx 形式则是在行首插入 1 到 6 个 # ，对应到标题 1 到 6 阶：

```
# 这是 H1
## 这是 H2
###### 这是 H6
```

##### 区块引用 Blockquotes：

通过使用'>'表示引用，同一个段落可以只在开头标示。多层引用通过多个'>'来标示。

```
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.
```

##### 列表：

* 无序列表，无序列表使用星号、加号或是减号作为列表标记(*、+、-)。

```
*   Red
*   Green
*   Blue
```

* 有序列表，数字、点、空格组成。1. 这样的格式。

```
1.  Bird
2.  McHale
3.  Parish
```

列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符。如果在航首不需要列表形式，可以在点前面加反斜杠（\）转义。

##### 代码：

开头用4个空格或者1个制表符，等同于html中的'pre'或'code'包裹。markdown也可以用`,``,3个点来包裹代码。

```
这是一个普通段落：
    这是一个代码区块。
```

##### 分隔线：

多个*或者-就能表示出分隔线。

```
* * *
***
*****
- - -
---------------------------------------
```

---------

#### 区段元素：

##### 连接：

* 行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可:

`This is [an example](http://example.com/ "Title") inline link.`

如果你是要链接到同样主机的资源，你可以使用相对路径：

`See my [About](/about/) page for details.`

* 参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记:

`This is [an example][id] reference-style link.`

`[id]: http://example.com/  "Optional Title Here"`

链接内容定义的形式为：

>* 方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
* 接着一个冒号
* 接着一个以上的空格或制表符
* 接着链接的网址
* 选择性地接着 title 内容，可以用单引号、双引号或是括弧包着

范例：

```
I get 10 times more traffic from [Google][] than from [Yahoo][] or [MSN][].

  [google]: http://google.com/        "Google"
  [yahoo]:  http://search.yahoo.com/  "Yahoo Search"
  [msn]:    http://search.msn.com/    "MSN Search"
```

##### 强调：

使用星号（*）和底线（_）作为标记强调字词的符号，被 * 或 _ 包围的字词会被转成用 `` 标签包围，用两个 * 或 _ 包起来的话，则会被转成 ``，

```
*single asterisks*
_single underscores_
**double asterisks**
__double underscores__
```

如果需要使用\*和\_的话，通过\来转义，或者`标示成代码。

##### 图片：

同超链接一样有行内式和参考式子，行内式例子如下：

```
![Alt text](/path/to/img.jpg)
![Alt text](/path/to/img.jpg "Optional title")
```

详细叙述如下：

>* 一个惊叹号 !
* 接着一个方括号，里面放上图片的替代文字
* 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上 选择性的 'title' 文字。

----------

#### 其他：


##### 自动连接：

对于< http://example.com/ >和< address@example.com\ >markdown会自动转成连接。

##### 反斜杠：

利用反斜杠来插入一些在语法中有其它意义的符号，如下：

```
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```

##### 表格：

在为知笔记中还支持如下方式表格：

```
| 为知笔记|客户端  | 版本 |
|------------|-----------|--------|
| WizNote   | Android| 6.1.8 |
```

具体效果如下：

| 为知笔记|客户端  | 版本 |
|------------|-----------|--------|
| WizNote   | Android| 6.1.8 |










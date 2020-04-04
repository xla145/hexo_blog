---
title: markdown的使用笔记
date: 2019-02-26 11:28:10
tags:
- markdown
- http
---
由于最近写博客需要，了解到一种轻量级标记语言-markdown。下面来说一下关于一些markdown的知识：
# 1. markdown是什么？
Markdown是一种轻量级标记语言，创始人为约翰·格鲁伯（英语：John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML（或者HTML）文档”。这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。
# 2. markdown的历史发展？
John Gruber 在 2004 年创造了 Markdown 语言，在语法上有很大一部分是跟亚伦·斯沃茨（Aaron Swartz）共同合作的。这个语言的目的是希望大家使用“易于阅读、易于撰写的纯文字格式，并选择性的转换成有效的XHTML（或是HTML）”。 其中最重要的设计是可读性，也就是说这个语言应该要能直接在字面上的被阅读，而不用被一些格式化指令标记（像是RTF与HTML）。 因此，它是现行电子邮件标记格式的惯例，虽然它也借鉴了很多早期的标记语言，如：Setext、Texile、reStructuredText。Gruber也编写了的Perl脚本：Markdown.pl，用于把markdown语法编写的内容转换成有效的、结构良好的XHTML或HTML内容，并将左尖括号<和&号替换成它们各自的字符实体引用。它可以用作单独的脚本，Blosxom和Movable Type的插件又或者BBEdit的文本过滤器。Markdown也已经被其他人用Perl和别的编程语言重新实现，其中一个Perl模块放在了CPAN(Text::Markdown)上。它基于一个BSD风格的许可证分发并可以作为几个内容管理系统的插件。
# 3. markdown能用来做什么？
由于Markdown的轻量化、易读易写特性，并且对于图片，图表、数学式都有支持，当前许多网站都广泛使用 Markdown 来撰写帮助文档或是用于论坛上发表消息。例如：GitHub、reddit、Diaspora、Stack Exchange、OpenStreetMap 、SourceForge等。甚至Markdown能被使用来撰写电子书。
# 4. markdown的一些语法和平时书写的注意点？
# 4.1. 标题
书写标题的时候，我们只需要在文本前面加上『# 』即可创建一级标题，如果想创建二级，三级，或者更多只需要增加『# 』的个数即可。下面展示一下效果：
		# 一级标题
		## 二级标题
		### 三级标题
		#### 四级标题
		##### 五级标题
		###### 六级标题
效果如下图：
![](https://img.mupaie.com/20190226100452277.png)
# 4.2. 引用

在需要的整个段落第一行最前面加上『>』即可：
	>在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	效果如下图：
	![在这里插入图片描述](https://img.mupaie.com/20190226101026545.png)
区块引用还可以嵌套使用
	>  在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	>  > 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	>  > > 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	![在这里插入图片描述](https://img.mupaie.com/20190226101213185.png)
	不仅可以嵌套使用，还可以再引用中加入标题，列表等元素：
	>  ## 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	>  > 1. 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	>  > 2.  在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
	效果如下图：
	![在这里插入图片描述](https://img.mupaie.com/20190226101413977.png)
# 4.3. 列表
列表在http中有无序和有序列表，markdown也有如下：
 __无序列表__ :使用星号 、加号或是减号来作为标记
	- test
	- test
	- test
效果如下图：
![在这里插入图片描述](https://img.mupaie.com/20190226102026437.png)
有序列表则是在文本前面加上数字和小数点
	1. test
	2. test
	3. test
效果如下图：
![在这里插入图片描述](https://img.mupaie.com/20190226102236789.png)
在列表中如何使用引用？
	*  在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
		> 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
		> 在这里插入图片描述 双通道（英语：Dual-channel）是一种能够让计算机性能增加的技术
在列表栏下，引用的文本需要缩进
效果如下图：
![在这里插入图片描述](https://img.mupaie.com/20190226102633553.png)
代办列表：表示列表数据是否被勾选
	- [ ] 不勾选
	- [x] 勾选
效果如下图：
![在这里插入图片描述](https://img.mupaie.com/20190226102826784.png)
# 4.4. 代码块
只要把代码块包裹在“` 之间，在围栏式代码块中，你可以指定一个可选的语言标识符，然后我们就可以为它启用语法着色了：

	```ruby
		require 'redcarpet'
		markdown = Redcarpet.new("Hello World!")
		puts markdown.to_html
	```

效果如下图：
	![在这里插入图片描述](https://img.mupaie.com/20190226103542307.png)
# 4.5. 强调
在Markdown中，可以使用 * 和  _  来表示斜体和加粗。
单个 * 和 _ 修饰，表示斜体，双个 * 和 _ 修饰表示粗体

	*Markdown*
	_Markdown_
	**Markdown**
	__Markdown__
效果如下：
![在这里插入图片描述](https://img.mupaie.com/20190226104023173.png)
# 4.6. 链接

	文字链接 [文字描述](地址)
	图片链接 ![文字描述](地址)
效果如下：
![在这里插入图片描述](https://img.mupaie.com/20190226104232855.png)
还有一些表格，一些工程图（流程图，时序图等）都可以使用markdown表现出来，我这里就不一一介绍了，具体参考[Markdown 语法介绍](https://coding.net/help/doc/project/markdown.html#i-6)
# 4.7. 换行：
&emsp;&emsp;1.使用两个以上的空格加回车
&emsp;&emsp;2.使用http标签 \<br>
# 4.8. 首行缩进
	&emsp;&emsp;1. 半角空格 “\&ensp;”
	&emsp;&emsp;2. 全角空格 “\&emsp;”
	&emsp;&emsp;3. 不换行空格 “\&nbsp;”
# 4.9. 文本加颜色
markdown 可以通过像html一样，可以通过标签样式让文本有一定的格式。

	<font color="red">test</font>

效果如下：
![在这里插入图片描述](https://img.mupaie.com/20190226111339130.png)
具体需要实现什么样式，参考[css文档](http://www.runoob.com/css/css-text.html)

# 5. 总结
今天简单介绍了markdown，并通过几个例子来展示具体用法。其实markdown还有很多内容，所以需要大家在以后使用中多积累多完善，这样才能更好的使用和掌markdown。

参考文档：
[Markdown 语法介绍](https://coding.net/help/doc/project/markdown.html#i-6)
[Markdown使用技巧总结——字体，颜色，字号，背景，首行缩进等](https://blog.csdn.net/u010177286/article/details/50358720)
[全角半角 && 6种空白空格的区别](https://blog.csdn.net/bao19901210/article/details/54625703)
[维基百科 markdown介绍](https://zh.wikipedia.org/wiki/Markdown)




---
layout: post
title: '区分URL和URI'
tags: JavaScript
---
前段时间在纠结于js中的[escape()](http://www.w3school.com.cn/js/jsref_escape.asp)、[encodeURI()](http://www.w3school.com.cn/js/jsref_encodeuri.asp)、[encodeURIComponent()](http://www.w3school.com.cn/js/jsref_encodeURIComponent.asp)他们之间的区别，后来就发散到了URI与URL上去了。

现在先把js中这三个函数的差别搞清楚吧！

1. escape()方法会对 ASCII 字母和数字以及下面这些 ASCII 标点符号：* @ - _ + . / 。__以外__的其他所有的字符转义序列替换。

2. encodeURI()方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号：;/?:@&=+$,#

3. encodeURIComponet()方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。但是对于其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号）讲由一个或多个十六进制的转义序列替换。

----

好了，下面来讲讲__URL(统一资源定位符)__和__URI(通用资源标志符)__

###URL

多数同学可能熟悉"URL"，而不是URI。其实URL是URI命名机制的一个子集。

URL是用于完整地描述Internet上网页和其他资源的地址的一种标识方法。

Internet上的每一个网页都具有一个唯一的名称标识，通常称之为URL地址，这种地址可以是本地磁盘，也可以是局域网上的某一台计算机，更多的是Internet上的站点。简单地说，URL就是Web地址，说白了其实URL就是__网址__。

URL的一般格式为(带方括号[]的为可选项)：

protocol :// hostname[:port] / path / [;parameters][?query]#fragment

###URI

Web上可用的每种资源 - HTML文档、图像、视频片段、程序等 - 由一个通用资源标志符（Uniform Resource Identifier, 简称"URI"）进行定位。

在HTML中，URI被用来：

* 链接到另一个文档或资源(参看A和LINK元素)。
* 链接到一个外部样式表或脚本(参看LINK和SCRIPT元素)。
* 在页内包含图像、对象或applet(参看IMAG、OBJECT、APPLET和INPUT元素)。
* 建立图像映射(参看MAP和AREA元素)。
* 提交一个表单(参看FORM)。
* 建立一个框架文档(参看FRAME和IFRAME元素)。
* 引用一个外部参考(参看Q、BLOCKQUOTE, INS和DEL元素)。
* 指向一个描述文档的metadata(参看HEAD元素)。
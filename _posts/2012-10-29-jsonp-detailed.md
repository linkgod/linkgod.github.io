---
layout: post
title: 'JSONP详解'
tags: JavaScript
---
#0x01 写作背景

第三期[极客大挑战](http://geek.myclover.org/)编程题里有一个修改User-Agent的题目，所以就想了想能不能用js来实现，但是出了很多问题，使得我回头阅读《JavaScript权威指南》，有了很多心的收获，特别是关于JSONP，所以就有了这篇博文。

#0x02 关于__同源策略__

同源策略是对JavaScript代码能够操作哪些Web内容的一条完整的安全策略。当Web页面使用多个`<iframe>`元素或者打开其他页面窗口的时候，这一策略通常就会发挥作用。在这种情况下，同源策略负责管理窗口或者窗体中的JavaScript代码以及和其他窗口或帧的交互。具体来说，__脚本只能读取和所属文档来源相同的窗口和文档属性__，而源包括协议、域名或者主机号、端口号。

#0x03 关于跨域AJAX请求

发起AJAX请求，我们通常使用XMLHttpRequest对象，但是它作为同源策略的一部分，通常只能够发起和文档具有相同服务器的HTTP请求。

而`<script>`元素的src属性能设置URL并发起HTTP GET请求。使用`<script>`元素实现脚本操作HTTP是很吸引人的，因为他们可以实现跨域通讯而不受限于同源策略，并且他可以直接对JSON编码的数据格式进行解码。

如果这种使用`<script>`元素作为AJAX传输的技术，并且通过HTTP请求的到的数据是__按照一定规则构造后__的JSON，这个技术就是传说中的__JSONP__(JavaScript Object Notation with Padding)。(_先不要纠结这一句，继续往下_)

#0x04 JSONP详解

我们先来看这样段示例代码：

```html
<!doctype html>
<html>
<head>
	<title>Just A Demo</title>
	<meta charset="utf-8">
</head>
<body>
	<script type="text/javascript">
	function showMessage(message){
		alert(message);
	}
	</script>
	<script type="text/javascript" src="http://xxx.xxx/data"></script>
</body>
</html>
```

data文件的内容是:

```js
showMessage('我来自火星！')
```

运行这段代码，会出现弹窗“我来自火星”。完毫无疑问，跨域调用成功了，我们成功传递了‘我来自火星’这个字符串。

但是这个跟JSON似乎不沾边呀！

现在对上面的示例代码进行一个小小的改动：

```html
<!doctype html>
<html>
<head>
	<title>Just A Demo</title>
	<meta charset="utf-8">
</head>
<body>
	<script type="text/javascript">
	function showMessage(data){
		alert(data.name);
		alert(data.description);
	}
	</script>
	<script type="text/javascript" src="http://xxx.xxx/data"></script>
</body>
</html>
```

data文件的内容还是:

```html
showMessage({'name': 'linkgod', 'description': 'A OK Man!'})
```

通过这个例子，我们不难理解JSONP中的P，["Padding：v.给…装衬垫，加垫子(pad的现在分词)"](http://dict.baidu.com/s?wd=padding)，使用括号`()`将JSON数据给装垫起来。

通过上个部分我们看到了JSONP的本质是使用`<script>`元素调用数据，最后响应的内容自动被构造成JavaScript代码。

__AJAX(Asynchronous JavaScript and XML)和JSONP在本质上是不同的，AJAX的核心是通过XmlHttpRequest对象进行HTTP请求，而JSONP的是通过`<script>`标签的src来调用服务器提供的js脚本。__

在了解清楚JSONP的原理之后，我们来写一个getJSONP函数，总不能每次都写一个`<script>`标签吧！

```html
<!doctype html>
<html>
<head>
	<title>Just A Demo</title>
	<meta charset="utf-8">
</head>
<body>
	<script type="text/javascript">
	//定义getJSONP函数
	function getJSONP (url, callback) {
		showMessage = function(response){
			try{
				callback(response);
			}finally{
				delete showMessage;
				script.parentNode.removeChild(script);
			}
		}
		var script = document.createElement("script");
		script.src = url;
		document.body.appendChild(script);
	}
	//执行JSONP请求
	getJSONP('http://xxx.xxx/data', function(data){
		alert(data.name);
		alert(data.description);
	})
	</script>
</body>
</html>
```

data文件的内容仍然是:

```js
showMessage({'name': 'linkgod', 'description': 'A OK Man!'})
```

但是每一次返回的JSONP前面还有一个方法名，我们必须跟写WebServer的工程师约定好方法名，或者就想办法告诉WebServer方法的名字。

这个可以通过在URL后面添加一个参数实现：例如“?jsonp”

```js
//代码来源于《JavaScript权威指南》
function getJSONP (url, callback) {
	//为本次请求创建一个唯一的回调函数名
	var cbnum = "cb" + getJSONP.couter++; //每次自增计数器
	var cbname = "getJSONP." + cbnum; //作为JSONP函数的属性

	if(url.indexOf('?') === -1)
	{
		url += "?jsonp=" + cbname;
	}else{
		url += "&jsonp=" +cbname;
	}

	var script = document.createElement("script");
	//定义将会被脚本执行的回调函数
	getJSONP[cbnum] = function(response){
		try{
			callback(response);
		}finally{
			delete getJSONP[cbnum];
			script.parentNode.removeChild(script);
		}
	}
	//触发HTTP请求
	script.src = url;
	document.body.appendChild(script);
}
getJSONP.couter = 0; //用于创建唯一回调函数名称的计数器
```

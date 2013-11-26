---
layout: post
title: 'HTML5 history API'
tags: JavaScript HTML5
---
window 对象通过history对象提供对览器历史记录的访问能力。它暴露了一些非常有用的方法和属性，让你在历史记录中自由前进和后退，而在HTML5中，更可以操纵历史记录中的数据。

-----

## history API概览

```js
//历史记录栈中一共有的页
history.length

history.back();
history.forward();

//go()从当前会话的历史记录中加载页面(当前页面位置索引值为0，上一页就是-1，下一页为1)
//类似的，传递参数“2”，你就可以向前移动2页。
history.go();


history.pushState();
history.replaceState();
```
------

## history.pushState() and history.replaceState()

因为XMLHttp的setRequestHeader()方法修改头，有些些头是无法修改的，其中包括referrer。(无法修改的头可以参考[http://www.linkgod.net/set-ajax-request-header.html](http://www.linkgod.net/set-ajax-request-header.html))

可以使用 history.pushState() 会改变 referrer 的值，而在你调用方法后创建的 [XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/DOM/XMLHttpRequest) 对象会在 HTTP 请求头中使用这个值。referrer的值则是创建 [XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/DOM/XMLHttpRequest) 对象时所处的窗口的URL。

打开[baidu](http://www.baidu.com)，在console中运行这段代码，然后查看network

```js
/**
 * 测试ajax的referrer是否改变
 */

history.pushState(null, null, 'linkgod');
var xmlHttpRequest = new XMLHttpRequest();

/**
 * open(method,url,async)
 * method：请求的类型；GET 或 POST
 * url：文件在服务器上的位置
 * async：true(异步)或 false(同步)
 */
xmlHttpRequest.open('get', 'http://www.baidu.com', true);
// setRequestHeader(header,value)
xmlHttpRequest.send();
```

![](http://m2.img.libdd.com/farm4/2013/0423/00/27A7DB40E94FAA4AD43005CDCB6CAD5BB557ABB0C1E16_299_196.jpg)

----

这两个方法允许逐条地添加和修改历史记录条目。这些方法可以协同[window.onpopstate](https://developer.mozilla.org/zh-CN/docs/DOM/window.onpopstate)事件一起工作。

### __pushState()__方法

pushState()有三个参数：一个状态对象、一个标题（现在会被忽略），一个可选的URL地址。下面来单独考察这三个参数的细节：

__状态对象（state object）__ — 一个JavaScript对象，与用pushState()方法创建的新历史记录条目关联。无论何时用户导航到新创建的状态，popstate事件都会被触发，并且事件对象的state属性都包含历史记录条目的状态对象的拷贝。

任何可序列化的对象都可以被当做状态对象。因为FireFox浏览器会把状态对象保存到用户的硬盘，这样它们就能在用户重启浏览器之后被还原，我们强行限制状态对象的大小为640k。如果你向pushState()方法传递了一个超过该限额的状态对象，该方法会抛出异常。如果你需要存储很大的数据，建议使用sessionStorage或localStorage。

__标题（title）__ — FireFox浏览器目前会忽略该参数，虽然以后可能会用上。考虑到未来可能会对该方法进行修改，传一个空字符串会比较安全。或者，你也可以传入一个简短的标题，标明将要进入的状态。

__地址（URL）__ — 新的历史记录条目的地址。浏览器不会在调用pushState()方法后加载该地址，但之后，可能会试图加载，例如用户重启浏览器。新的URL不一定是绝对路径；如果是相对路径，它将以当前URL为基准；传入的URL与当前URL应该是同源的，否则，pushState()会抛出异常。该参数是可选的；不指定的话则为文档当前URL。

某种意义上，调用pushState()有点类似于设置window.location='#foo'，它们都会在当前文档内创建和激活新的历史记录条目。但pushState()有自己的优势：

* 新的URL可以是任意的同源URL，与此相反，使用window.location方法时，只有仅修改 hash 才能保证停留在相同的document中。
* 根据个人需要来决定是否修改URL。相反，设置window.location='#foo'，只有在当前hash值不是foo时才创建一条新历史记录。
* 你可以在新的历史记录条目中添加抽象数据。如果使用基于hash的方法，你只能把相关数据转码成一个很短的字符串。

### __replaceState()__方法

history.replaceState()操作类似于history.pushState()，不同之处在于replaceState()方法会修改当前历史记录条目而并非创建新的条目。

当你为了响应用户的某些操作，而要更新当前历史记录条目的状态对象或URL时，使用replaceState()方法会特别合适。

### popstate事件

每当激活的历史记录发生变化时，都会触发popstate事件。如果被激活的历史记录条目是由pushState所创建，或是被replaceState方法影响到的，popstate事件的状态属性将包含历史记录的状态对象的一个拷贝。

假如当前网页地址为http://example.com/example.html,则运行下述代码后:

```js
window.onpopstate = function(event) {
  alert("location: " + document.location + ", state: " + JSON.stringify(event.state));
};
//绑定事件处理函数.
history.pushState({page: 1}, "title 1", "?page=1");    //添加并激活一个历史记录条目 http://example.com/example.html?page=1,条目索引为1
history.pushState({page: 2}, "title 2", "?page=2");    //添加并激活一个历史记录条目 http://example.com/example.html?page=2,条目索引为2
history.replaceState({page: 3}, "title 3", "?page=3"); //修改当前激活的历史记录条目 http://ex..?page=2 变为 http://ex..?page=3,条目索引为2
history.back(); // 弹出 "location: http://example.com/example.html?page=1, state: {"page":1}"
history.back(); // 弹出 "location: http://example.com/example.html, state: null
history.go(2);  // 弹出 "location: http://example.com/example.html?page=3, state: {"page":3}
```

即便进入了那些非pushState和replaceState方法作用过的(比如http://example.com/example.html)没有state对象关联的那些网页, popstate事件也仍然会被触发.

-----

资料：

[https://developer.mozilla.org/zh-CN/docs/DOM/Manipulating_the_browser_history](https://developer.mozilla.org/zh-CN/docs/DOM/Manipulating_the_browser_history)

[https://developer.mozilla.org/zh-CN/docs/DOM/window.onpopstate](https://developer.mozilla.org/zh-CN/docs/DOM/window.onpopstate)

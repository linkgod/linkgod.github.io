---
layout: post
title: 'CSS定位：子元素将相对于父元素定位'
tags: CSS
---
CSS元素定位postion中只有

* static：无特殊定位，对象遵循正常文档流。top，right，bottom，left等属性不会被应用。
* relative：对象遵循正常文档流，但将依据top，right，bottom，left等属性在正常文档流中偏移位置。
* absolute：对象脱离正常文档流，使用top，right，bottom，left等属性进行绝对定位。而其层叠通过z-index属性定义。
* fixed：对象脱离正常文档流，使用top，right，bottom，left等属性以窗口为参考点进行定位，当出现滚动条时，对象不会随着滚动。IE6及以下不支持此参数值

-----------------

如果子元素要实现相对于父元素定位使用：

如果给父元素(`li`)定义为`position:relative`;

子元素(`#menu2`，`#menu3`)定义为`position:absolute`，那么子元素的位置将相对于父元素，而不是整个页面。

```html index.html
<!doctype html>
<html>
<head>
    <title>LinkGod</title>
    <meta charset="utf-8">
    <link rel="stylesheet" href="../css/page.css">
</head>
<body>
    <nav>
        <ul id="menu1">
            <li>Home</li>
            <li>Web
                <ul id="menu2">
                    <li>HTML</li>
                    <li>CSS</li>
                    <li>jQuery</li>
                    <li>JavaScript
                        <ul id="menu3">
                            <li>JavaScript基础</li>
                            <li>BOM</li>
                            <li>DOM</li>
                        </ul>
                    </li>
                </ul>
            </li>
            <li>javaScript</li>
            <li>Contact</li>
        </ul>
    </nav>
    <script type="text/javascript">
    (function(){
        var oLi = document.getElementById('menu1').getElementsByTagName('li');
        var i = 0;
        for(; i < oLi.length; i++){
            // console.info(oLi[i]);
            oLi[i].onmouseover = function(){
                if (this.children.length > 0) {
                    this.children[0].style.display = "block";
                };
            }
            oLi[i].onmouseout = function(){
                if (this.children.length > 0) {
                    this.children[0].style.display = "none";
                };
            }
        }
    })();
    </script>
</body>
</html>
```

```css page.css
@charset "utf-8";

*{margin: 0px; padding: 0px;}
nav{
    width: 485px;
    margin: 20px auto;
}
ul{
    width: 160px;
    list-style: none;
    border: 1px solid #DDD;
    padding: 0px;
}
li{
    position: relative;
    border-bottom: 1px dashed #DDD;
    padding-left: 20px;
    line-height: 42px;
}
li:last-child{
    border-bottom: none;
}
li:hover{
    background-color: #CC99FF;
}
#menu2, #menu3{
    display: none;
    position:absolute;
    top:-1px;
    left:160px;
}
```
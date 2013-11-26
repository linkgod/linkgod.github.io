---
layout: post
title: 'SeaJS入门笔记'
tags: JavaScript SeaJS
---
# 资料 [http://seajs.org/docs/#quick-start](http://seajs.org/docs/#quick-start)

# 1. 在页面中引入seajs模块

在 hello.html 里，与脚本相关的代码只有一行：

```js
< script
src = "assets/seajs/sea.js" -- sea.js 路径
data - config = "hello/config" -- 给 SeaJS 用的项目配置文件
data - main = "hello/main" > -- 项目的起始模块
< / script >
```

sea.js 在下载完成时，会根据 data-config 自动获取配置，然后加载 data-main 指定的模块。

# 2. 配置文件config.js

```js
seajs.config({
   // 加载 shim 插件
  plugins : [ 'shim' ],

   // 配置别名,   alias别名的意思
  alias : {
     'jquery': {
         src: 'common/jquery-1.9.1.min.js',
         exports: 'jQuery'
     }
  }
});
```

# 3. 模块代码

```js
// 所有模块都通过 define 来定义
define( function (require, exports, module) {

   // 通过 require 引入依赖
   var $ = require( 'jquery' );
   var Spinning = require( './spinning' );

   // 通过 exports 对外提供接口
  exports.doSomething = ...

   // 或者通过 module.exports 提供整个接口
  module.exports = ...

});
```

-----

API快速参考 [https://github.com/seajs/seajs/issues/266](https://github.com/seajs/seajs/issues/266)

seajs有7个接口要闹记于心

* seajs.config
* seajs.use
* define
* require
* require.async 异步
* exports
* module.exports

----

# CMD模块定义规范 [https://github.com/seajs/seajs/issues/242 ](https://github.com/seajs/seajs/issues/242)

# define define(id?, deps?, factory)

define 也可以接受两个以上参数。字符串 id 表示模块标识，数组 deps 是模块依赖。比如：

```js
define( 'hello' , [ 'jquery' ], function (require, exports, module) {

   // 模块代码

});
```

id 和 deps 参数可以省略。省略 id 参数时，SeaJS在加载时会用模块文件的访问路径作为模块标识。省略 deps 参数时，SeaJS会自动提取 factory 函数中的 require('string') 来获取模块依赖。

__在开发阶段，推荐不要手写 id 和 deps 参数，因为这两个参数可以在构建阶段通过工具自动生成。__

经常使用的 API 只有 __define, require, require.async, exports, module.exports__ 这五个。其他 API 有个印象就好，在需要时再来查文档，不用刻意去记。

----

# seajs配置项 [https://github.com/seajs/seajs/issues/262](https://github.com/seajs/seajs/issues/262)

常用的配置项是 alias、 plugins

plugins有一个 shim插件，关于shim插件可以参考 [https://github.com/seajs/seajs/issues/579](https://github.com/seajs/seajs/issues/579)

SeaJS 中的模块默认都遵守 CMD 规范，但现实中已存在大量普通 JavaScript 类库，比如 jQuery、Underscore 等。使用 shim 插件，可以在运行时将这些普通 JS 文件转换成 CMD 模块，从而能在 SeaJS 中正常使用。参见上文的配置文件config.js

----

# seajs的基础路径（base路径）

模块系统的基础路径即`base`的默认值，与`sea.js`的访问路径相关：

    如果 sea.js 的访问路径是：
      http : //example.com/assets/sea.js
    则 base 路径为：
      http : //example.com/assets/
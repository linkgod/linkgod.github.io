---
layout: post
title: 'Mongoose快速入门'
tags: JavaScript Nodejs
---
Mongoose是MongoDB的一个对象模型工具，可以工作于异步环境下。

## 创建连接

```javascript
var mongoose = require('mongoose'),
	db = mongoose.connection;

mongoose.connect('mongodb://localhost/nodeblog');

db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function callback () {
	console.log('connect OK');
});

```

## 定义一个模型很容易：

Everything in Mongoose starts with a Schema. Each schema maps to a MongoDB collection and defines the shape of the documents within that collection.

The permitted SchemaTypes are

* String
* Number
* Date
* Buffer
* Boolean
* Mixed
* ObjectId
* Array

```javascript
var blogSchema = new Schema({
	title:  String,
	author: String,
	body:   String,
	comments: [{ body: String, date: Date }],
	date: { type: Date, default: Date.now },
	hidden: Boolean,
	meta: {
		votes: Number,
		favs:  Number
	}
});
```

## 添加一个实例方法

```javascript
// NOTE: 方法必须添加给schema，然后再用mongoose.model()编译它
var animalSchema = new Schema({ name: String, type: String });

animalSchema.methods.findSimilarTypes = function (cb) {
	return this.model('Animal').find({ type: this.type }, cb);
}

var Animal = mongoose.model('Animal', animalSchema);
var dog = new Animal({ type: 'dog' });

dog.findSimilarTypes(function (err, dogs) {
	console.log(dogs); // woof
});
```

## 添加一个静态方法
```javascript
animalSchema.statics.findByName = function (name, cb) {
  this.find({ name: new RegExp(name, 'i') }, cb);
}

var Animal = mongoose.model('Animal', animalSchema);
Animal.findByName('fido', function (err, animals) {
  console.log(animals);
});
```
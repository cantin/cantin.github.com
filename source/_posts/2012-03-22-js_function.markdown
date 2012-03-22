---
layout: post
title: "new-post"
date: 2012-03-22 22:49
comments: true
categories: javascript
---

###前言

最近在重新读“javascript高级程序设计”，又有一些体会，故而写下来。
顺带一提，这本书真的相当不错。

###object

js中有undefined，null，boolean，number，string五种基本类型，再加上object这个最根本的复杂（引用）
类型。其中基本类型是以栈存放，而object是以堆存放。

在js中object就是key: value的集合。

function对象也是object对象，但是他们都是以function关键字定义滴。

```js
  Object //function Object() {};
  Function //function Function() {};
```

每一个object里面都会有个constructor的key，value一般就是object的function引用。
例如：

```js
 var o = new Obkect();
 o.constructor // function Object() {}
```
###function, new 与Object, Function
 function与Function是不同的，function是关键字，而Function是对象。

```js
function s() { this.qq = 'ss' }
s  //function s() { this.qq = 'ss' }
s.constructor // function Function() {}
```
所有的function对象都是由Function这个对象创建出来的。

```js
 function s() { this.qq = 'ww' };
 var o = new s();
```

与下面代码应该相等：

```js
 function s() { this.qq = 'ww' }
 var o = {};
 s.call(0)
 o.constructor = s
 0.__proto__ = s.prototype
```
```js
 function s() { this.qq = 'asd' };
 s.call(s);
 s.qq;     // 'asd'
```


###__proto__

object都有__proto__属性，其中保存着constructor的prototype

```js
 //在chrome下
 function s() {}
 s.__proto__  // function Empty() {}
 Function.prototype // function Empty() {}
 Function.__proto__ // function Empty() {}
```

###prototype

function对象的属性存放处

###function, object
object拥有constructor，__proto__属性
function除了拥有上述属性，还拥有prototype属性,还拥有call，apply方法

###后记
写的还真烂······

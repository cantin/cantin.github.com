---
layout: post
title: "js与observer"
date: 2012-03-11 20:41
comments: true
categories:  'javascript'
---

##前言

放假的晚上，窝在宿舍，懒懒的。无聊之余就想写一下之前学过的
javascript的observer，免得自己将来真的忘了。
ps：现在真的快忘了。。。。

##javascript与observer

###为什么需要observer

在编写js的时候，通常会遇到这样的问题。js中的数据与页面的数据需要保持一致.
比如说在js中的一个model，当它发生改变时，页面上对应的数据必须发生改变。
如果不使用observer的话，我们可能需要自己手动的在方法中调用。这样会导致model
与view的代码紧耦合。一旦需求变动，将很难改动。在这种情况下，我们可以使用observer来
解决这个问题。通过model的注册事件，一旦发生事件，对应的view的代码便会执行，从而达到
一致的目的。

###observer的用法

####Event对象

首先，我们需要一个Event对象来存放事件触发后执行的handler与它的scope。
event构造如下：

```js
  function Event(handler, scope, options) {
    this.handler = handler;
    this.scope = scope;
    this.options = options;
  }
```

####model的写法

在model中，需要有属性如下：
```js
 function Model(options) {
  var event = ["add", "remove", "update"];
  var listener = {};

  ....
 }
```
其中event为数组，存放着当model的事件，当这些事件触发时，会调用listener中的handler。
listener是存放handler的一个对象（或者近似的认为是hash）。
在model里还需要有如下方法：

```js
  Model.pototype = {
    add: function() {
      .....

      // at the end
      this.fireEvent('add')
    }

    on: function(event, handler, scope, options) {
      if(this.hasEvent(event)){
        var e = new Event(handler, scope, options);

        if(!this.listener[event]){
          this.listener[event] = new Array();
        }

        this.listener[event].push(e);
      }
    },
    
    //private
    fireEvent: function(event) {
      if(this.hasEvent(event)){
        for(var i = 0; i < this.listener[event].length; i++){
          var e = this.listener[event][i];
          e.handler.call(e.scope, options)
        }
      }
    },

    //private
    hasEvent: function(event) {
      for(var i = 0; i < this.event.length; i++){
        if(this.event[i] == event) {
          return true;
        }
      }
      return false;
    }
  }
```
其中private表示此方法不对外提供,
hasEvent是为了判断传入的事件是否是model中注册的事件,
fireEvent是在model在内部调用，用以调用在view在model注册的handler,
on是提供对外的接口，用来让view调用，注册view的handler，
add是model事件函数示例，在让model发生改变的方法中的最下面调用fireEvent，从而达到调用handler的目的。

ps:不负责任的说，Event对象的options参数是我在写这篇文章时候才加进去的，没有经过任何测试，哇哈哈。
ps:再不负责的说，上述方法都是我凭印象写的，正确性应该是可以保证的吧····

####view的写法
在view中我们需要向model中注册当model发生改变是自动执行的函数，也就是handler.
示例代码如下：

```js
  function View(model) {
   this.model = model;
  }
  var model = new Model();
  var view = new View(model);

  view.addView = function() {
    //something for handle while model fire
    .....
  };

  view.addHandler = function() {
    this.model.on('add', addView, this);
  };

  view.addHandler();
```
view的写法有多种，大意就是调用model的on函数。

###小结
observer的好处在于，当model（或者是其他对象）变化的时候，它能通知到其他注册在上面的对象也发生变化。
这样一方面是全部对象更新的很及时，另一方面是解耦了对象，model（被观察对象？）与view（观察对象？）只需要通过
model的几个函数交互，而不需要知道其他对象的实现细节,同时view可以注册多个handler到model中去，可扩展性比较好。
ps:我还是不知道model与view是谁观察谁·····
ps：文笔感觉还是很不行，见谅见谅。

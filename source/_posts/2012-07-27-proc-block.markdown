---
layout: post
title: "proc block"
date: 2012-07-27 21:23
comments: true
categories: ruby proc block 
---

###Proc

在ruby中，block是一种隐形的方法参数, 而不是对象，在方法中直接调用不到，
，但是在方法中可以将其转化为proc对象，从而可以使用proc的方法。


###&操作符

block转化为proc的方法是&操作符

####block转化proc

```ruby
def method(&block)
  puts block.class
end

method {}

=> Proc
```

此时的{} 是隐形的参数，method的表面参数是为0，如果传入的是proc对象，
proc对象是表面的参数，则会报ArgumentError: wrong number of arguments (1
for 0)

####proc转化为block

```ruby
def method
  yield if block_given?
end

p = proc { puts 'proc' }

method &p

=> proc
```

此时的proc经过&转换，在method中变为block隐形的参数。
另：&p不可以单独使用, 不可在表达式中使用，如

```ruby
&p
=> syntax error, unexpected tAMPER

1 == 1 ? &p : 1
=> syntax error, unexpected tAMPER
```

但是可以

```ruby
self.send(:method, &p)

=> proc
```

###结语

proc，lamdba，block还是很多不得不说的事....


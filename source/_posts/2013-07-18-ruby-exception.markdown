---
layout: post
title: "ruby raise exception"
date: 2013-07-18 21:40
comments: true
categories: ruby
---

###Exception
 Exception是ruby中代表异常的对象。它的实例方法有：

``` ruby
1.9.3p429 :002 > Exception.instance_methods(false)
 => [:exception, :==, :to_s, :message, :inspect, :backtrace, :set_backtrace]
```

 其中：

`message`方法会调用`to_s`方法，用以返回exception的信息。

`backtrace`返回exception的调用栈，显示形式如下： ｀Filename: linenumber in methodname`

`exception`方法比较有趣，当没有参数或者是参数就是exception本身时，它会返回exception本身这个对象，
否则它会调用参数的`to_str`方法并创建一个新的exception对象。

```
  1.9.3p429 :003 > a = ArgumentError.new
   => #<ArgumentError: ArgumentError>
  1.9.3p429 :004 > a.object_id
   => 70171868342380
  1.9.3p429 :005 > a.exception.object_id
   => 70171868342380
  1.9.3p429 :006 > a.exception(a).object_id
   => 70171868342380
  1.9.3p429 :007 > b = a.exception('test')
   => #<ArgumentError: test>
  1.9.3p429 :008 > b.object_id
   => 70171868160920
```

`exception`方法是ruby与其他语言的异常处理不同之处。下文将会介绍。


###Raise
`Raise`是Kernel的一个方法，用于抛出Exception。

`raise`有几种调用方式：

####1.无参数调用

  `raise`会产生一个新的RuntimeError对象抛出

```
1.9.3p429 :013 > raise
RuntimeError:
  from (irb):13
  from /Users/can/.rvm/rubies/ruby-1.9.3-p429/bin/irb:16:in `<main>'
```

####2.传入一个参数

#####2.1参数为String

`raise`会将创建一个新的RuntimeError，并将string参数作为这个对象的message。

```
1.9.3p429 :017 > raise 'test'
RuntimeError: test
  from (irb):17
  from /Users/can/.rvm/rubies/ruby-1.9.3-p429/bin/irb:16:in `<main>'
```

####2.2 参数为Exception Class

`raise`会调用其new方法，创建一个新的Exception对象

```
1.9.3p429 :018 > raise ArgumentError
ArgumentError: ArgumentError
	from (irb):18
	from /Users/can/.rvm/rubies/ruby-1.9.3-p429/bin/irb:16:in `<main>'
```

####2.3 参数为Exception Object

  `raise`会调用这个object的exception方法，返回exceptin方法生成的exception对象

```
class ArgumentError
  def exception(string = self)
    puts 'exception happen'

    super string
  end
end

raise ArgumentError.new

#output
exception happen
exception.rb:57:in `<main>': ArgumentError (ArgumentError)
```

###3.传入2-3个参数

####3.1 2个参数

 在2.2－2.3中介绍了，如果第一个参数为Class的话会调用new，为Object的话会调用到exception

 而第二个参数会被作为exception的message传入exception的new／exception方法中

```
class ArgumentError
  def initialize(string = '')
    puts 'Initialize happen'

    super string
  end
  def exception(string = self)
    puts 'exception happen'

    super string
  end
end

begin
  raise ArgumentError, 'test'
rescue => e
  puts e
end

begin
  raise ArgumentError.new, 'test'
rescue => e
  puts e
end

#output
Initialize happen
test
Initialize happen
exception happen
test
```

####3.2 3个参数

 第三个参数是指定backtrace, 也就是上面所说的调用栈



###Rescue StandardError rather than Exception

`rescue`默认是处理StandardError。


StandardError是Exception的子类，表示的是在ruby程序中可预见的，比较普通的exception,
如ArgumentError之类的应该被ruby程序处理的异常。

Exception下面还有了其他错误类，如SystemExit,
SystemStackError,这些代表了一些更底层，更严重的，恢复可能性更小的exception。
一般来说，ruby程序不应该试图处理这些错误。

当我们自定义Exception时，最好也是继承于StandardError。

而且，rescue时最好不要`rescue Exception`，因为它会捕捉到其他的如SystemExit这样的错误。


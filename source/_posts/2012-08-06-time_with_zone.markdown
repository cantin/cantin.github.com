---
layout: post
title: time_with_zone
date: 2012-08-06 21:04
comments: true
categories: rails time
---

###time_with_zone

在rails中，server time跟传入的time参数在转化有时候会造成一些问题。

###time

rails默认使用utc的时区存放time，但是在server中需要使用时区设置转换成本地时间

比如: utc为：'2012-08-08 12:00:00',
而时区设置是：config.time_zone = 'Beijing', 即'+0800'
那么当我们在console取出来事：'2012-08-08 20:00:00 +0800'

但是，有时候也会出现不一致的情况

###time params with zone

```ruby
params[:time] = '2012-08-08 12:00:00 +0700'
```

当上述params传入并save时，在console中取到的：

```ruby
time = '2012-08-08 13:00:00 UTC'

#changed to local_time

time = '2012-08-08 21:00:00'
```

时间就多了一个小时，这是因为time中的'+0700'

当attribute是datetime时，rails会调用Time.zone.parse(original_time)与time.in_time_zone
[link](https://github.com/rails/rails/blob/master/activerecord/lib/active_record/attribute_methods/time_zone_conversion.rb#L59)

当没有时区参数的时候，time默认是utc的时区，所以当本地时区是+0800时,
它会减去8小时，将time从utc格式转换为+0800时区的格式。

而当有参数时(如+0700)，time就是+0700时区的，会从+0700转换成+0800, 然后rails取出数据的话
默认是使用utc取出的，即会加上8小时,所以time就出现了错误。

如果要加上+0700的话，应该在application.rb中添加:

```ruby
config.active_record.default_timezone = '#something respond to +0700'
```

然后存入跟取出的time就会一致了

###后记

这个是time存入跟取出使用了两种不同时区而导致的错误




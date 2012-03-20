---
layout: post
title: "spork reload model"
date: 2012-03-20 22:33
comments: true
categories: spork
---

####spork的问题

使用spork1.0.0rc2在为测试加速时，并不会每次都reload model。
也就是说，使用spork时，如果你model有了一丝改变，那就需要重启spork。
老实说，这相当不方便。

####解决方法1
(使用的是rspec)在spec_helper.rb里面添加如下代码：

```ruby
Spork.each_run do
  require 'factory_girl_rails'
  # reload all the models
  Dir["#{Rails.root}/app/models/**/*.rb"].each do |model|
    load model
  end
end
```

###解决方法2
前提是使用rspec与factory_girl_rails
在gemfile里面添加：

```ruby
gem 'factory_girl_rails', require: false
```

在spec_helper.rb里添加：

```ruby
Spork.each_run do
  require 'factory_girl_rails'
end
```

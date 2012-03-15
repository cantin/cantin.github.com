---
layout: post
title: "generator on rails"
date: 2012-03-15 22:27
comments: true 
categories: rails generator
---

###前言
generator在rails开发中是十分好用的工具,
今晚看了一下rails的generator，闲来无事便将基本的记下来。

guides的链接为[http://guides.rubyonrails.org/generators.html](http://guides.rubyonrails.org/generators.html)

rails3.0以后的generator是建立在Thor这个gem上的，github的链接为[https://github.com/wycats/thor](https://github.com/wycats/thor)

###生成generator

进入rails项目，在终端下输入下列命令:

```ruby
rails generate generator generator_name
```

生成文件如下:

```sh
/lib/generator/generator_name/USAGE
/lib/generator/generator_name/generator_name.rb
/lib/generator/generator_name/templates/
```

其中generator_name.rb中是执行generate命令时运行的文件，
USAGE是这个generator的说明，
templates目录下是执行这个generator是需要用到的文件,可在generator_name.rb中改变

若generator_name.rb中有copy_file这个方法
当执行generator命令时，此方法会找到templates下的相应文件copy到rails项目中

具体可以参考guides

###配置generator

在rails中可以在config/applications.rb配置generator，如下：

```ruby
config.generators do |g|
  g.orm             :active_record
  g.template_engine :erb
  g.test_framework  :test_unit, :fixture => true
end
```

###后记
目前还未有用到generator的机会，所以写的实在不是很详细，等以后再来完善吧。

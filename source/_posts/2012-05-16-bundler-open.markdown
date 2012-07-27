---
layout: post
title: "bundler open"
date: 2012-05-16 22:37
comments: true
categories: bundler tips
---

####bundler

bundler open 选项可以打开选定的local gem，
但是在使用的时候需要先指定$EDITOR或者$BUNDLER_EDITOR。

这时候可以先执行
```
export EDITOR=vim
```
然后执行

```
bundle open gem_name
```

这样就可以在vim中打开gem_name所在的目录了



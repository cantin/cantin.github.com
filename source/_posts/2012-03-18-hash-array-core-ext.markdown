---
layout: post
title: "monkeypatch with hash and array"
date: 2012-03-18 22:00
comments: true
categories: ruby core-ext
---

###前言

几个月前，工作中需要判断一个一个hash中是否包含了另一个hash/array(在更深层次上)，
但ruby中没有提供这样的方法，所以在涛哥的指导下，我写了下列两个方法来
实现这个功能

###array

array的方法如下：

```ruby
class Array
  def contains? array
    array.each do |value|      
      flag = false
      self.each do |v|
        if  value.class == v.class && v.respond_to?(:contains?)
          flag = true if v.contains?(value)
        else
          flag = true if v == value       
        end
      end
      return false unless flag
    end

    true
  end
end
```

###hash

hash的方法如下：

```ruby
class Hash
  def contains?(hash)
    hash.each do |key, value|  
      return false unless include?(key)

      if value.respond_to?(:contains?)
        return false unless self[key].contains?(value)
      else
        return false unless value == self[key] 
      end
    end

    true
  end
end
```

###后记

第一次写ruby的extsion，印象还是很深刻的。

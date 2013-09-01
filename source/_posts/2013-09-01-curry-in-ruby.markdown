---
layout: post
title: "Currying in Ruby"
date: 2013-09-01 13:26
comments: true
categories: Ruby
---

###Currying
 [Currying](http://en.wikipedia.org/wiki/Currying) is the technique of transforming a function that takes multiple arguments (or a tuple of arguments) in such a way that it can be called as a chain of functions, each with a single argument .

For instance:

```js
function add(x, y, z) {
  return x + y + z;
}

function add5(y, z) {
  return add(5, y, z);
}

function add5and6(z) {
  return add5(6, z);
}

add5and6(10) => 21
```
###Currying in Proc of Ruby

In Ruby, Object is the first class, but we still can use it as FP.

Proc is kind of like function, Ruby provide a method named currying in Proc.
This give us a easy way to currying proc like currying function.

Baisc Usage: 
```ruby
add = proc { |x, y, z| x + y + z }
add5 = add.curry[5]
add5and6 = add5.curry[6] # equal to add.curry.call(5).call(6)

add5and6.(10) => 21
```

Proc#curry also can take one argument(arity) that limit the quantity of arguments.

For instance: 

```ruby
add = proc { |x, y, z| x + y + (z || 0) }
add5 = proc.curry(2)[5]
add5and6 = add5.curry[6] => 11
# z will be ignored cause by the arity is 2
```

Note: the arity argument in Lambda#curry must equal to the number of arguments in Lambda, otherwise it will raise a ArgumentError.

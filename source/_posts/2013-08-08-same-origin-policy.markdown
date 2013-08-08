---
layout: post
title: "Same Origin Policy"
date: 2013-08-08 19:19
comments: true
categories: 
---

###Same Origin Policy(同源策略）


同源策略是浏览器用来防止恶意javascript执行的策略。

同源策略意为：只有在同源下的JS对象才可以互相访问，操作。

源的意思是指js所在的页面，如test.html中通过jquery CDN引入的jQuery对象，
它的源是test.html，而不是script的src: 'code.jquery.com'。

同源的意思是指拥有相同的协议，域名，端口。

JS对象的意思是所有BOM,DOM,以及所有的自定义对象。


例子：

我们在localhost:3000/test.html引入远程文件jQuery.js，
并再嵌入一个iframe， src为'127.0.0.1:3000/frame.html'。

``` html
<html>
  <body>
    <script src='http://code.jquery.com/jquery-1.8.3.js' type="text/javascript" charset="utf-8"></script>
    <script type="text/javascript" charset="utf-8">
      jQuery(function() {
        console.log(window.frames[0].location);
      });
    </script>

   <iframe src="127.0.0.1:3000/iframe.html" frameborder="0"></iframe>
  </body>
</html>
```

因为localhost与127.0.0.1在同源判断上是两个不同的源，所以
`window.frames[0].location`会报错，并且返回空的location

```
Warning:

Blocked a frame with origin "http://localhost:3000" from accessing a frame with origin "http://127.0.0.1:3000". Protocols, domains, and ports must match.

Return: 

Location {} 
```

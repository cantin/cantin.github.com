---
layout: post
title: "jquery-rails-monkey-patch"
date: 2012-11-26 17:57
comments: true
categories: rails monkey_patch
---

##Jquery-rails monkey patch

Since Rails 3, unconspicuous javascript(jquery-rails) instead of prototype.js to make
thing easily. It provide a sample way to handle ajax, confirm, etc...

Sometimes jquery-rails couldn't fit for our situation.For example:

With added "confirm: 'this is is a confirm'", the button element
has the confirm function. But We could't dynamically set the text whlie
the confirm appear.

In this case, We can add a monkey patch into jquery-rails to fit for our need.

First of all We look at the source of juqery-rails, figure out what make
this happened.

```ruby
  allowAction: function(element) {
    var message = element.data('confirm'),
        answer = false, callback;
    if (!message) { return true; }

    if (rails.fire(element, 'confirm')) {
      answer = rails.confirm(message);
      callback = rails.fire(element, 'confirm:complete', [answer]);
    }
    return answer && callback;
  },
```

After that, We overwrite this function in application.js 

```ruby
  $.rails.allowAction = function(element) {
    var rails = $.rails;

    var message = element.data('confirm'),
        answer = false, callback;
    if (!message) { return true; }

    if (rails.fire(element, 'confirm')) {
      message = element.data('confirm');
      answer = rails.confirm(message);
      callback = rails.fire(element, 'confirm:complete', [answer]);
    }
    return answer && callback;
  }
```

Finally, We can dynamically set the confirm text.

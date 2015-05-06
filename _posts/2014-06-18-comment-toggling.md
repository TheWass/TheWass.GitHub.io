---
layout: post
title: "Comment Toggling"
date: 2014-06-18 20:42:08
modified: 2015-02-27

categories: []
tags:
  - Code Tricks
  - Debugging
---

I came across a nifty way of quickly toggling code:  
Simply, add a slash to comment/uncomment a block.

* * *

Toggle one block:

{% highlight php linenos startinline %}
/*
Commented code here
//*/

//*
Active code here
//*/
{% endhighlight %}

* * *

Toggle two blocks:

{% highlight php linenos startinline %}
/*
Commented code here
/*/
Active code here
//*/

//*
Active code here
/*/
Commented code here
//*/
{% endhighlight %}

* * *

Perfect for testing two different implementations without worrying about tagging every line.

---
layout: post
title: "PHP: Order of Precedence"
date: 2014-05-30 20:06:47
modified: 2014-09-15

categories:
  - PHP
tags:
  - Code Tricks
  - Order of Precedence
  - PHP

excerpt: "I've run into a couple of interesting PHP features, mainly concerning order of precedence:"
---
I've run into a couple of interesting PHP <strike>bugs</strike>
features, mainly concerning order of precedence:

## The tl;dr version:

*   Assignment (`=`) has a _lower_ precedence than the symbol (`||`), but _higher_ than the keyword (`or`).
*   If the left side of `or` is true, then the right side is not evaluated at all. 
    *   Similarly, the right side of `and` is not evaluated if the left is false.
*   In PHP, `(bool) 0` evaluates to `false`, and every other number (including -1) evaluates to `true`.
*   Assignments themselves evaluate to the value, not the variable.
*   Great PHP testing website: [http://writecodeonline.com/php/](http://writecodeonline.com/php/)

* * *

## The lengthy, long, thanks-for-sticking-around version:

Consider this code:

{% highlight php linenos startinline %}
$x = false OR true;
$y = false || true;
var_dump ($x);
var_dump ($y);
{% endhighlight %}

Here's what gets dumped:

{% highlight php startinline %}
=> bool(false) bool(true)
{% endhighlight %}

### WTF?

Turns out, unlike other languages (that I'm familiar with), the keyword `or`
actually has a lower precedence than the assignment operator.
This causes the assignment `$z = $y` to be evaluated before the or operation.
However, the or symbol (`||`) has a higher precedence than assignment, and
functions as expected.

Something to watch out for (or abuse ☺).

* * *

Here's a more extended example:

{% highlight php linenos startinline %}
var_dump($x = 1 || $y = 0);
var_dump($x);
var_dump($y);
echo "\n";
var_dump($x = 1 or $y = 0);
var_dump($x);
var_dump($y);
{% endhighlight %}

This prints:

{% highlight php startinline %}
bool(true) bool(true) NULL
bool(true) int(1) NULL
{% endhighlight %}

### SRSLY? WTF?

Let's step through what this is doing, but first, some things to note:

*   Operator precedence for this case: 
    1.  `||`
    2.  `=`
    3.  `or`
*   Using inline assignments like this is probably not a good idea in actual code. It just obfuscates it. 
    *   For this example, it's a good way to see if the evaluation took place or not.

1.  `$x = 1 || $y = 0;`
    *   Assignment is right associative, so everything to the right of each assignment operator is grouped.
2.  `$x = (1 || $y = (0));`
    *   Assignment is evaluated last, so the assignment operations are grouped first.
3.  `($x = (1 || ($y = (0))));`
    *   Next, the `||` operation takes precedence, and is left associative.
    *   Everything to the left of the `||` operation is grouped. (the `||` operation is already grouped.)
4.  `($x = ((1) || ($y = (0))));`
    *   Now, evaluate the expression starting with the first complete sub-expression.
5.  `($x = ((1) || ($y = (0)))); //evaluate 1`
6.  `($x = (1 || ($y = (0)))); //evaluate ||`
    *   If the left side of `||` is true, then the right side is not evaluated, and `$y` remains null.
7.  `($x = true); //evaluate =`
8.  `true;`

The end state of the first part is:

{% highlight php startinline %}
$x => true
$y => NULL
{% endhighlight %}

The next statement is more simplistic:

1.  `$x = 1 or $y = 0;`
    *   In this case, or is evaluated last, and is left associative.
2.  `(($x = 1) or $y = 0);`
    *   Next, the assignments take precedence. They are right associative.
    *   `$x = 1` is already grouped, but `$y = 0` needs a group.
3.  `(($x =(1)) or ($y = (0)));`
    *   Now the groups are evaluated, once again, starting with the first complete sub-expression.
4.  `(($x =(1)) or ($y = (0))); //evaluate 1`
5.  `(($x = 1) or ($y = (0))); //evaluate =`
6.  `(1 or ($y = (0))); //evaluate or`
7.  `true;`
    *   Since the left side ends up being true, the entire or statement is true. No need to evaluate the right side, and `$y` remains null once again.

The end state of the second part is:

{% highlight php startinline %}
$x => 1
$y => NULL
{% endhighlight %}

For completeness's sake, here's the evaluation if `$x` is assigned to 0 instead of 1 (making the `or` statement false):

1.  `(($x =(0)) or ($y = (0))); //evaluate 0`
2.  `(($x = 0) or ($y = (0))); //evaluate =`
3.  `(0 or ($y = (0))); //evaluate or`
    1.  `(false or ($y = (0))); //Since left is false, eval right.`
    2.  `(false or ($y = (0))); //evaluate 0`
    3.  `(false or ($y = 0)); //evaluate =`
4.  `(false or 0); //evaluate or`
5.  `false;`

The end state of this is:

{% highlight php startinline %}
$x => 0
$y => 0
{% endhighlight %}

* * *

Moral of the story? Use parentheses!  
Hopefully, you learned something new from reading this, I definitely learned a few things from writing it.  
The more you know...

---
layout: post
title: "PHP Arrays"
date: 2014-07-31 16:34:17
modified: 2015-02-04

categories:
  - PHP
tags:
  - Arrays
  - Code Tricks
  - PHP
---
Today's rant is on PHP arrays and constants!  
There are a lot of things that I like about PHP: its C-like code structure,
its versatility, its support in the community, its web framework (Laravel, not CI).
But there is one thing in PHP that I absolutely abhor:  
**Arrays.**

For some insane, stupid reason, Arrays are treated more like functions
than actual first-class variables. Even the structure gives it away:

{% highlight php linenos startinline %}
$array = array(1, 2, 3);
{% endhighlight %}

Look at that junk, it's a friggin _function call!_

Because of this, PHP won't let you assign arrays to constants.
_Even arrays with constant elements._

{% highlight php linenos startinline %}
const MY_ARRAY = array(1, 2, 3);
//=>; PHP Fatal error: Arrays are not allowed in class constants
{% endhighlight %}

### SRSLY?? WTF??

Okfine, class constants. Let's try outside the class:

{% highlight php linenos startinline %}
define('MY_ARRAY', array(1, 2, 3));
//=> PHP Warning: Constants may only evaluate to scalar values
{% endhighlight %}

### WHAA???

**_What kind of a !@#$%^ language does that??_**

* * *

After researching how to best emulate a constant array, I've come up with a method
that I think emulates the properties of a constant the closest:

{% highlight php linenos startinline %}
class Constants
{
    public static function MY_ARRAY($index = false) {
        $arrayData = array(1, 2, 3);
        return $index !== false ? $arrayData[index] : $arrayData;
    }

    var_dump(self::MY_ARRAY());  //Dumps the entire array
    var_dump(self::MY_ARRAY(0)); //Dumps the first element

}
var_dump(Constants::MY_ARRAY());
var_dump(Constants::MY_ARRAY(0));
{% endhighlight %}

The array itself is encapsulated in a static function.
It’s called almost the same way as a regular constant,
just with a pair of parens on the end.

It’s not the pristine, immutable `self::MY_ARRAY` that I want, but it works.

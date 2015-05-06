---
layout: post
title: "PHP Ghost Methods"
date: 2014-08-11 20:22:55
modified: 2015-02-04

categories:
- PHP
- Meta-Programming Series
tags:
- Magic Methods
- Meta-Programming
- PHP

---
This is part two of three in the PHP Meta-Programming series. This part discusses the remainder of PHP's Magic Methods; what I have termed 'ghost' methods and parameters.

* * *

## Ghost Properties

Ok, now we're finally getting to the _really_ fun stuff.

The next four methods are really simple to understand, which by extension means really simple to abuse. Here are the definitions right from the [documentation](http://php.net/manual/en/language.oop5.overloading.php#object.set):

*   `__set()` is run when writing data to inaccessible properties.
*   `__get()` is utilized for reading data from inaccessible properties.
*   `__isset()` is triggered by calling `isset()` or `empty()` on inaccessible properties.
*   `__unset()` is invoked when `unset()` is used on inaccessible properties.

So it says inaccessible properties.
This means private properties, protected properties, or properties that _don't even exist_.

I'll let that sink in for a minute.

The obvious use case: I have a class that stores and associates data. But I have no idea what data is going to be stored at runtime.
Begin magic:

{% highlight php linenos startinline %}
class Data
{
    private $data = array();

    public function __set($property, $value)
    {
        $this->data[$property] = $value;
    }

    public function __get($property)
    {
        if (array_key_exists($property, $this->data)) {
            return $this->data[$property];
        }
    }

    public function __isset($property)
    {
        return isset($this->data[$property]);
    }

    public function __unset($property)
    {
        unset($this->data[$property]);
    }
}
{% endhighlight %}

Now, the object can store and retrieve _any_ number of properties of _any_ datatype.

{% highlight php startinline %}
$obj = new Data;
$obj->someRandomPropertyThatIsDefinitelyNotDefined = 'Oh, but it is now!';
echo $obj->someRandomPropertyThatIsDefinitelyNotDefined;
//=> Oh, but it is now!
{% endhighlight %}

This is why I call them 'ghost' properties.
Strictly speaking, they are properties that are defined _at runtime_.  
Try doing _that_ in C.

Here's a slightly less obvious use case:  
I want to add some error checking to all my private properties,
but I don't want to write 50 setters and getters all with the exact same code.
So, let's write 1 setter, and 1 getter to handle EVERY property:

{% highlight php linenos startinline %}
class Data
{
    private $a;
    private $whole;
    private $slew;
    private $of;
    private $private;
    private $properties;

    public function __set($name, $value)
    {
        if (<>) {
            throw new Exception; //invalid data
        }
        if (property_exists($this, $name)) {
            $this->$name = $value;
        } else {
            throw new Exception; //to alert the caller.
        }
    }

    public function __get($name)
    {
        //add special get code here
        //or, simply...
        return $this->$name;
    }
}

$instance = new Data;
$instance->a = 'somestring';
echo $instance->a;
$instance->bad = 'badstring';
echo $instance->bad;

/*
OUTPUT:
somestring
Fatal error:  Uncaught exception 'Exception'....
*/
{% endhighlight %}

And if you didn't think that was crazy enough...

* * *

## Ghost Methods

Believe it or not, the same techniques that we used on properties
can also be applied to methods.
There are two functions: `__call` and `__callStatic`.

Whenever an inaccessible instance method is called, `__call` is called instead.
Same goes for static methods but `__callStatic` is called.

Use cases on this are a little bit harder to find, but the idea that you can handle
_any_ method in instance or class is a powerful tool to have.

Let's set up a scenario: Say I have a class that handles storing lists in a file.
I want to make a nice interface to all of those lists. I know how they are stored,
but I have no idea what those lists are called ahead of time.  
Here's the basic format of the file:

{% highlight text %}
AwesomeList
item1
item2
item3
item4

LessAwesomeList
item1
item2
item3
item4
{% endhighlight %}

Linefeeds function as delimiters, and if there are two linefeeds in a row,
then it's a new list.
I won't write actual PHP (far too lazy), but I can show how
the magic works through pseudocode.

{% highlight text linenos %}
public function getList($list)
    open file
    find list
    close file

public function getItem($list, $item)
    getList($list)
    if $item is integer, assume array index
        find $list[$item]
    if $item is string, do string comparison
        find $item in $list

// Now for the magic!
public function __call($name, $args)
    if $name starts with 'get'
       if $args is empty
            call getList($name-'get')
        else
            call getItem($name-'get', $args[0])
    else
        method not found exception
{% endhighlight %}

So, if I were to call `$obj->getAwesomeList(3)` it would return `item4` as expected.

`$obj->getAwesomeList($arg1)` is an inaccessible function, so the interpreter
does this instead: `$obj->__call('getAwesomeList', array(3));`
The `__call` function parses the method name and args, pulls out the get, and calls
`getItem('AwesomeList', 3)`.

That's just _freaking awesome_!

* * *

Onto [Part 3!]({% post_url 2014-08-11-php-coding-techniques %})

---
layout: post
title: "PHP Meta-Programming"
date: 2014-08-05 20:11:26
modified: 2015-04-17

categories:
- PHP
- Meta-Programming Series
tags:
- Magic Methods
- Meta-Programming
- PHP

---
I've been looking at ways to improve my PHP code, writing less copy-paste code, and
attempting to get the line counts down, all while learning more and more about PHP.
Writing all this down turned out to be a massive undertaking, so I have split it into
three parts. This is part one of three.  
This part gives an overview of some of PHP's Magic Methods.

* * *

This topic is massive, way more massive than I could hope to cover in just one post,
so I'll be splitting it into three parts.

The first two parts, I'll cover what PHP calls 'Magic Methods'. The third part will be
more miscellaneous coding techniques to attempt to avoid Copy-Paste Syndrome, or to
make code look a little nicer.

Most of these functions were added for PHP version 5.3.
I'll assume you're working with version 5.3.27 or higher.
Exceptions will be noted.

* * *

# Magic Methods (pt1)

The official PHP documentation _actually_ calls them magic methods.
So, yeah, they're magical!

On the realistic side of things, they are class methods that trigger when something
happens, like when calling a method or property that doesn't exist,
or when the instance of that class is echoed.

There are 14 magic methods documented in PHP. I'll go over all of them,
but I'll focus on a couple that I think can be 'abused' the most.
You'll find them in part 2 of this series.

Here are their signatures in the form: `[static] returnType functionName ( parameters )`

{% highlight php startinline %}
void __construct ([ mixed $args = "" [, $... ]] )
void __destruct ( void )
void __clone ( void )

array __sleep ( void )
void __wakeup ( void )

string __toString ( void )
static object __set_state ( array $properties )

mixed __get ( string $name )
void __set ( string $name , mixed $value )
bool __isset ( string $name )
void __unset ( string $name )

mixed __call ( string $name , array $arguments )
static mixed __callStatic ( string $name , array $arguments )
mixed __invoke ([ $... ] )
{% endhighlight %}

I have grouped similar functions together, and I will address them in order.

* * *

## Object Creation/Destruction

For those of you who are familiar with OOP, this will be a little boring,
but bear with me for a minute.

All objects should be able to be created, copied, and deleted safely and completely.
In the world of compiled programming, this is an absolute must, since a malformed
constructor/destructor pair could easily crash your program at least, and brick your computer
at worst. There is less of a concern with interpreted languages, since the interpreter will
just kill itself before the computer is harmed.

Bottom line, the `__construct`/`__clone`/`__destruct` functions are not necessarily
needed in PHP, but it's probably good to at least have a constructor to set default
instance variables if nothing else. A good use case for all three would be
establishing a connection to a database. You'd want to make sure that the
instance gets a proper connection, does not allow that instance to be copied,
and can completely terminate that connection.

* * *

## Object Serialization

Sometimes, objects need to be stored somewhere. Object Serialization, in essence, hashes
the object, putting it in a kindof 'stasis' to be thrown on disk, or in a file, etc.

The `__sleep` and `__wakeup` methods are called right before an object is serialized or
unserialized respectively.

Use case for this, going back to the database, you'd want to make sure that the connection
gets properly closed and reopened before and after serialization.

* * *

## Object Outputs

There are multiple ways to output the current state of an object.
PHP provides three functions to facilitate this.

`__toString` is simple: It returns a string whenever a string is needed. `toString` allows you to `echo $obj;`
without having to do some nasty type conversions beforehand. The only requirement is
that the return statement must be a string. I would recommend having this for every
class you make. It's a lifesaver when debugging.

`__set_state` is a little different. It triggers when an object is passed to `var_export()`
Since this method is static, it is not bound to any particular instance of that class.
TBH, I haven't found a reason to use it. Though, interestingly enough, `var_export()`
returns _executable_ code. Abuse as you will.

`__invoke` is a fun little function. It allows you to call the instance itself as a function.
As an example it enables you to do this:

{% highlight php startinline %}
$object = new Test;
$object('param'); //$object->__invoke('param') is called.
{% endhighlight %}

* * *

In the next part, I'll be taking about accessing inaccessible methods and properties.
It's pretty awesome! Click the link below!  
[Ghost Methods]({% post_url 2014-08-11-php-ghost-methods %})
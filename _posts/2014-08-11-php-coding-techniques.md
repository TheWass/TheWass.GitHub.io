---
layout: post
title: "PHP Coding Techniques"
date: 2014-08-11 20:26:45
modified: 2014-11-21

categories:
- PHP
- Meta-Programming Series
tags:
- Meta-Programming
- PHP

---
So, warning before you begin. This one's a wee bit lengthier than the other two.
I thought I was almost done, then I found something else that was just too Wassome(tm)to leave out.

I really should trademark that term...

This is part 3 of a series on PHP Meta-Programming.

As a reminder, these techniques work in PHP version 5.3.27 or newer.
Your mileage may vary on older versions.

* * *

## Variable-Length Argument Lists

Ever want to be able to handle an indefinite amount of arguments?
Passing in an array is cumbersome, since you need to create the array
to send it to the function, then parse through it once it's in the function,
adding extra overhead.

Luckily for us, PHP has built-in methods capable of providing information
about the arguments that are passed in:

*   mixed func_get_arg ( int $arg_num )
*   array func_get_args ( void )
*   int func_num_args ( void )

They're fairly self explanatory, and they allow for variable-length argument lists.

The way to use them is simple. PHP really doesn't care how many arguments you pass
to a function. It won't yell at you at all if you pass in to many arguments to a function;
it does express displeasure if you pass too few. PHP will still run your script
as best it can with the arguments you give it. Quite forgiving.

{% highlight php linenos startinline %}
function foo()
{
    $numargs = func_num_args();
    echo "Number of arguments: $numargs\n";
    if ($numargs >= 2) {
        echo "Second argument is: " . func_get_arg(1) . "\n";
    }
    $arg_list = func_get_args();
    for ($i = 0; $i < $numargs; $i++) {
        echo "Argument $i is: " . $arg_list[$i] . "\n";
    }
}
foo ('a', 'b', 'c');
/* This prints:
Number of arguments: 3
Second argument is: b
Argument 0 is: a
Argument 1 is: b
Argument 2 is: c
*/
{% endhighlight %}

### Special PHP 5.6 Note:

PHP 5.6 streamlines the argument list feature by adding some syntactic sugar to
replace those three functions.
In PHP 5.6 you can now declare a parameter to be an argument list by prepending
it with an ellipsis (...).
It'll automatically convert all the arguments into a single array.

Here's the same function using the ellipsis syntax:

{% highlight php linenos startinline %}
function foo(...$args) //PHP 5.6 only
{
    //At this point, you can use $args just like any other array.
    $numargs = count($args);
    echo "Number of arguments: " . $numargs . "\n";
    if ($numargs >= 2) {
        echo "Second argument is: " . $args[1] . "\n";
    }
    for ($i = 0; $i < $numargs; $i++) {
        echo "Argument $i is: " . $args[$i] . "\n";
    }
}
{% endhighlight %}

* * *

## Method Overloading

Here's what PHP.net has to say on [overloading](https://php.net/manual/en/language.oop5.overloading.php){:target="_blank"}:

> Overloading in PHP provides means to dynamically "create" properties and methods.
>  These dynamic entities are processed via magic methods one can establish in a class
>  for various action types.

This is what I called ghost methods and properties in the previous section.
I have no idea why they decided misuse the term 'overloading'. But, regardless,
I am _not_ using their definition.
I want to focus on actual OOP method overloading:

Multiple methods with the same name, but different arguments.

The way to accomplish this is to abuse Variable-Length Argument Lists a bit. We have a
'controller' function and a handful of 'sub functions' that the controller calls to
do the actual work.
This is useful if you have multiple versions of a constructor, especially if you need
to do some processing on some of the arguments before they become properties.

{% highlight php linenos startinline %}
class OverloadingExample
{
    //Variable-Length Argument Lists
    public function __constructor()
    {
        switch (func_num_args()) {
            case 0:
                //Assign some random properties
                break;
            case 1:
                if (is_numeric(func_get_arg(0))) {
                    doSomething(func_get_arg(0));
                } else {
                    doSomethingCompletelyDifferent(func_get_arg(0));
                }
                break;
            default:
                doAnotherSomething(func_get_args());
                //Remember, a little bit of abuse, PHP functions don't care if you
                //give them more arguments than they ask for.
                break;
        }
    }
    private function doSomething($arg)
    {
        //some work
    }
    private function doSomethingCompletelyDifferent($arg)
    {
        //some different work
    }
    private function doAnotherSomething($argInt, $argStr)
    {
        //slack off, and do nothing.
    }
}
{% endhighlight %}

If you've got one-liners in the sub functions, there's really no reason
to keep them outside of the constructor _IF_ they're not needed anywhere else.

This technique is not exclusive to constructors, any function can be a controller.
Just use a switch on `func_num_args()` to call other functions.

* * *

## Method Chaining

Method chains are useful when constructing multiple unique complex objects.
Laravel and CodeIgniter's Active Record implementations are excellent examples
of great use cases for this. They deal in constructing complex SQL queries and running
them against a database.

**Note:** Method chaining only works in PHP 5\. Previous versions return objects by value.
Returning objects by reference makes this work.

Here is a basic SQL string builder class.

{% highlight php linenos startinline %}
class SQLBuilder
{
    private $select;
    private $from;
    private $where;

    public function __construct()
    {
        $this->select = '*';
        $this->from = '*';
        $this->where = null;
    }

    public function __toString()
    {
        $str = 'SELECT ' . $this->select;
        $str .= ' FROM ' . $this->from;
        if (!is_null($this->where))
        {
            $str .= ' WHERE ' . $this->where;
        }
        $str .= ';';
        return $str;
    }

    public function __get($name)
    {
        return $this->$name;
    }
{% endhighlight %}

To illustrate method chaining, I'll write two functions,
one that supports method chaining, and one that does not:

{% highlight php linenos startinline linenostart=30 %}
    //Without method chaining (Naive function: No error/type checking. Copier beware!)
    public function __set($name, $value)
    {
        if (property_exists($this, $name)) {
            $this->$name = $value;
        } else {
            throw new Exception; //to alert the caller.
        }
    }

    //Supports method chaining (also naive.)
    public function __call($method, $args)
    {
        //detect a setter
        if (substr($method, 0, 3) == 'set') {
            //extract property name
            $property = strtolower(substr($method, 3));
            $this->__set($property, $args[0]);
        } else {
            throw new Exception;
            // You should always throw some exception if __call
            // doesn't know what to do with a passed function.
        }
        return $this;
    }
}

$noChain = new SQLBuilder;
$noChain->select = 'id';
$noChain->from = 'table1';
echo $noChain;

$chain = new SQLBuilder;
echo $chain->setSelect('id')->setFrom('table1');
{% endhighlight %}

* * *

I've decided to give you a break and leave my final thoughts for a separate page.  
[Click here]({% post_url 2014-08-19-final-thoughts %})!
---
layout: post
title: 'Iterations: ''for'' or ''foreach''?'
date: 2015-02-04 16:42:56
modified: 2015-02-04

categories:
- PHP
tags:
- Code Tricks
- PHP

---
Those of you who first learned programming (me included) probably started learning with C++ or Java. And with these languages, iteration is typically done with `while` or `for` loops. The `foreach` construct is used for specific object classifications. As you might expect, these constructs may not operate in the same manner. Let me share with you the similarities and differences between `while`, `for`, and `foreach`.

## While Loops

a `while` loop is the basic iteration structure. It contains a block of code that is repeatedly executed while some condition remains true. Consider this code:  
{% highlight php linenos startinline %}
$count = 10;
while ($count > 0) {
    echo $count . "\n";
    $count--;
}
 echo "Blastoff!!"
{% endhighlight %}

This is a very trivial example. This code outputs a countdown from 10 to 1 and outputs "Blastoff!!" at the end. As you can see, once the end brace is reached, it loops back to the top of the loop and then checks the condition again. Once the condition becomes false, the code block is skipped entirely.  
 Now, this is probably second nature to all of you, but bear with me, you might learn something!

## For loops

Consider this code:
{% highlight php startinline %}
for ($count = 10; $count > 0; $count--) {
    echo $count . "\n";
}
echo "Blastoff!";
{% endhighlight %}
Compare this code to the while loop version. Not only do they do the exact same thing; they are interpreted the _exact same way_. The for loop is syntactic sugar for the while loop. Let me explain:  
 The compiler takes the components of the for loop and executes them in a specific order:

1.  assignment
2.  condition: if false, goto end; if true, continue
3.  body
4.  increment
5.  goto condition
6.  end

This:
{% highlight php startinline %}
for (assignment; condition; increment) {
    body
}
{% endhighlight %}
Changes to this:  
{% highlight php startinline %}
assignment;
while (condition) {
    body
    increment
}
unassign //the variable that was assigned.
{% endhighlight %}

The main difference between a for loop and a while loop is this: In a while loop, the assignment happens before the code block is executed. This means the counter is accessible _after_ the while loop has completed. The for loop automatically gets rid of the counter variable.

For loops were designed to simplify iterating over an array:  
{% highlight php startinline %}
$data = array('Bob', 'Tom', 'Joe', 'Sam', 'Ann');
for ($i = 0; $i < sizeOf($data); $i++) {
    echo $data[$i];
}
{% endhighlight %}

This code (as much as I [hate]({% post_url 2014-07-31-php-arrays %}) PHP arrays) is easy to read and understand. It just simply echos out each name in the data array. The while version is a little more difficult to parse through, but still readable:  
{% highlight php startinline %}
$data = array('Bob', 'Tom', 'Joe', 'Sam', 'Ann');
$i = 0;
while ($i < sizeOf($data)) {
    echo $data[$i];
    $i++;
}
unset($i);
{% endhighlight %}
This, once again, is a trivial example. What if that array contained more arrays or objects that you had to loop through. Now you have to deal with nested for loops, and you have to keep track of multiple count variables. What if I told you there is a better way? A way which brings object oriented programming into the mix:

## Foreach loop

The foreach loop operates entirely different than the procedural loops. Foreach leverages object oriented principles to carry out iteration. This makes it ideal for iterating on arrays of objects, or containers. For this to work, the container object must implement the iterable interface. For most programming languages, this means the container class must have these methods:

*   mixed current() - returns the current value/object.
*   scalar key() - returns the current index.
*   void next() - advances the iterator.
*   void rewind() - resets the iterator to the beginning.
*   boolean valid() - returns boolean true if the iterator is on a valid object.

All five of these methods are invisible to the programmer using the object. Foreach leverages them to perform iteration. In some languages, the array construct has these functions built in. Consider this code:  
{% highlight php startinline %}
$data = array('Bob', 'Tom', 'Joe', 'Sam', 'Ann');
    foreach ($data as $name) {
    echo $name;
}
{% endhighlight %}
Notice that it is even simpler than a for loop. No mucking about with a count variable. Also, for loops _require_ the array's index to be in order. Foreach loop doesn't care.  
`$data = array(10=>'Bob', 3=>'Tom', 247=>'Joe', 74=>'Sam', 26=>'Ann');`  
This will work just as well in a foreach loop; it is extraordinarily difficult for a for loop to iterate over this (xtra credit!).  
Here are the function calls that a foreach loop makes:

1.  rewind
2.  valid (break if false)
3.  current
4.  key (optional, only called if used in the body)
5.  next
6.  goto valid

The fundamental distinguishing feature of a foreach loop is that valid returns true or false if the pointer is on a valid object. No counting needed; just keep going until there is no more to go through.

The foreach construct looks cleaner and runs quicker than a for loop, especially when dealing with arrays. In all loops, a comparison is run at every single iteration. For arrays, this comparison is most often comparing the size of the array with the current index. More often than not, this is a relatively expensive operation which tends to grow linearly with size, and it is _calculated every single time_. Whereas the foreach loop checks validity, a very simple, fast operation.  
Foreach does fall short when modifying the array it is iterating over. This involves a lot of functional overhead to keep track of the dynamically changing values.

See [phpbench.com](http://www.phpbench.com) for more epic php benchmarks!

## tl;dr

When looping through php arrays:  
If you are modifying the array that you are looping through, use a for loop.  
Otherwise, use a foreach loop.

The more you know...

* * *

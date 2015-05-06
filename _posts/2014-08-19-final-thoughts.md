---
layout: post
title: "Final Thoughts"
date: 2014-08-19 20:32:01
modified: 2014-09-15

categories:
- PHP
- Meta-Programming Series
tags:
- Meta-Programming
- PHP

---
In my humble opinion, these techniques, magic methods, and ghost methods and properties
are all friggin _awesome!_ It really opens up what you can accomplish in PHP.
But, there's a catch. Really, it comes down to this: "With great power comes great
responsibility." For instance: When using `__call()`, `__set()`, and `__get()`,
you'll need to seriously document the _snot_ out of them.

* * *

Going through the previous two posts,
I've used `__call()` twice, both for different applications: once as a setter,
and once as a getter. It is completely legal to use `__call()` as both a
getter and a setter in the same function. In fact, You can also make `__call()`
a controller as shown earlier with the `__constructor()` overload example.
You can chain literally anything you please in the `__call()` function by using a
switch, or a series of ifs and have it be the ultimate controller into your class,
handling any sort of dynamic call that you deem it worthy to handle.

Just for kicks and giggles, here's the _ultimate example of awesome:_
A PHP interface for the _entire_ [World of Warcraft RESTful API](http://blizzard.github.io/api-wow-docs){:target="_blank"}  
(I actually did this in Ruby for a school project)

I picked this particularly because the WoW API covers the vast majority of data
and metadata contained within the _entirety_ of World of Warcraft.
Every spell, every player, every item, every guild, even an hourly auction house
dump for each realm is accessible from the API. This is a _massive_ amount of data.

And I have ~10 lines that can access it _all_:

{% highlight php linenos startinline %}
class WoWAPI
{
    const HOST = 'http://us.battle.net';

    public function __call($name, $args)
    {
        $url = self::HOST . '/api/wow';
        //strip off get_
        if (substr($name, 0, 3) == 'get') {
            $name = substr($name, 3);
            if (substr($name, 0, 1) == '_') {
                $name = substr($name, 1);
            }
        }
        //Slice the command up, and append the args
        $command = array_merge(explode('_', $name), $args);
        //construct the URL
        foreach ($command as $segment) {
            $url .= strtolower("/$segment");
        }
        echo $url; //for debugging purposes: http://www.hurl.it
        //send the get request and return the body
        //For the WoW API, everything returns as a JSON string.
        return http_parse_message(http_get($url))->body;
    }
}

//usage:
$wowApi = new WoWAPI;
echo $wowApi->getData_character('races');
echo "\n";
echo $wowApi->getSpell(8056);
echo "\n";
echo $wowApi->get_Achievement(2144);
echo "\n";
echo $wowApi->realm_status();
{% endhighlight %}

Seriously. That one little `__call()` function can handle _every single_ RESTful API
call that Blizzard supports, _and_ any additional features that they make available
in the future. If I put more time into it, I could make it a little more customizable,
and more programmer friendly. But this is just a example. :smiley:

The point is, those 10 or so lines cover a _vast_ amount of actual functional commands
to the World of Warcraft RESTful API.  
None of which can or will be automatically documented by a documentation generator.  
None of which will show up when the class itself is interrogated for functions.  
None of which will appear in an IDE to help others figure out the correct signature.  
Quite literally, Ghost Methods.

With great power comes great responsibility.

* * *

I hope you all enjoyed this rather unintentionally long series on
PHP Meta-Programming. If you have any questions about what I discussed in the series,
let me know, I'll provide as much help as I can.

-The Wass

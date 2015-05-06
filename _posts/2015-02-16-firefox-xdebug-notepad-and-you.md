---
layout: post
title: Firefox, Xdebug, Notepad++, and You!
date: 2015-02-16 15:21:12
modified: 2015-02-17

categories:
- PHP
tags:
- Debugging
- HowTo

---
Picture this: It's a dark and stormy Friday afternoon when all of the sudden, the PHP code that you've worked tirelessly on no longer works correctly. For some reason, the program is taking correct values and outputting junk. Unfortunately, due to complex requirements, and tight schedule, you didn't have time to put a verbose logging system into the program, and you're stuck var_dumping to the webpage.  
 Has anyone experienced this or something like it? I have, and I swore to put an end to the echoing madness!

After scouring the interwebz for answers, I found this PHP extension called [Xdebug](http://xdebug.org/download.php). Xdebug is a PHP extension designed to assist in debugging and profiling PHP code. It comes packaged with breakpoints, variable peeking and watching. It even supports debugging remotely. "This is perfect!", I thought, "I can install this and debug server code on my desktop!" Those of you who know Xdebug have probably already tried this, and are thinking "No, Wass. Don't bother. We've all tried it, and it's just too much of a pain to get around the port forwarding and the firewall." However, I tried anyway. And guess what??

[![nppoutput]({{ site.url }}/images/nppoutput.png?w=287)]({{ site.url }}/images/nppoutput.png)  
***I SUCCEEDED!***

And now, I shall share my knowledge and my setup:  
 So, basically, I've connected Xdebug on the server to Notepad++ on the desktop using ssh remote port forwarding. [This article](http://csce.uark.edu/~kal/info/private/ssh/ch09_02.htm) goes in depth about port forwarding, and the difference between the local and remote variants. Using remote forwarding, I've linked port 9000 on the server with port 9000 on the client tunneling through the ssh port 22; this entirely bypasses the RIS Firewall and its address translation. I use PuTTY to tunnel the port, but any SSH client with remote port forwarding will do the trick.  
Here is the ssh command:  
`ssh -R 9000:localhost:9000 ServerName`  
Here is a screenshot of the tunnel configuration:  
[![puttycfg]({{ site.url }}/images/puttycfg.png)]({{ site.url }}/images/puttycfg.png)  
In essence, what this does is it listens on the server's port 9000 and redirects any traffic on that port to localhost:9000\. It's a little confusing, but in this context localhost refers to the _client_, not the server.

* * *

###Server Side###  
Now, let's talk server requirements: Gotta get Xdebug. `yum install php-pecl-xdebug` should do the trick, or you can just pecl it. Go [here](http://xdebug.org/docs/install) for more info. Since Xdebug is a PHP Module, you can verify that it is correctly installed by running `php -m` on your server terminal. If Xdebug appears in _both_ [PHP Modules] and [Zend Modules] You're in good shape! Next step is to verify that Xdebug is set up and working in your php.ini file.  
Make sure these lines are in there:
{% highlight php startinline %}
xdebug.remote_enable=1
xdebug.remote_handler="dbgp"
xdebug.remote_host=127.0.0.1
xdebug.remote_port=9000
xdebug.remote_mode=req
xdebug.idekey=default
{% endhighlight %}
There are more settings that can enable and configure more features of Xdebug, but these are the required ones to get the remote debugging working. Notice that the remote host is the loopback address. This is intentional, since we are tunneling the connection through SSH, and not actually connecting to the remote host directly.

* * *

###Client Side###  
I use Notepad++ as the debugging client, but other text editors/IDEs will work too. Click [here](http://xdebug.org/docs/remote) to see more compatible editors. So long as the editor has DBGP capability, it should work.

First, the client needs to have access to the files that you will be debugging. This can be accomplished in a few ways: Samba share, WinSCP, FTP, are all viable methods. Even straight copying the files from the server to the client will work. The main requirement is having both a remote path (path that the server understands) and a local path (path that the client understands) to the files you wish to debug. If your local and remote files are copied rather than linked, make sure they are synchronized when you debug.  
To accomplish this, I use [NppFTP](http://ashkulz.github.io/NppFTP/). This is a Notepad++ plugin that offers similar functionality to WinSCP, just built right into Notepad++. I have configured NppFTP to cache server files in a local directory, then use that cache as the required local path. This keeps the two sets of files synched with each other.

Secondly, the client needs to have DBGP capability. Click [here](http://xdebug.org/docs/remote) to see more compatible editors. This is Xdebug's remote communication protocol. Notepad++ has a [DBGp plugin](http://sourceforge.net/projects/npp-plugins/files/DBGP%20Plugin/) which understands the protocol, and allows communication between it and Xdebug on the server. Once you have installed your supported editor/plugin, go to the configuration and set these properties:  
 * Set the Remote Server IP to 127.0.0.1  
 * Set the IDE Key to match what you set on the server (in this case: default)  
 * Set the remote path and local paths to match whatever your synchronization software is configured.  
 Remember, we are using SSH to tunnel the connection. That's why we are pointing the client at itself.

* * *

###All Together Now!###  
In order to start the debugging session, make sure the DBGP client in the editor is listening, and open a web browser to the page you want to debug. Next add this parameter: `XDEBUG_SESSION_START=idekey` to the webpage URL. Remember to change idekey to whatever you set the IDEKey on the server to. Alternatively, you can install [The Easiest Xdebug](https://addons.mozilla.org/en-US/firefox/addon/the-easiest-xdebug/) extension for Firefox, and it'll handle everything for you.

Once everything is said and done, Here's how it all works:  
 1\. SSH -R sets up remote forwarding from server:9000 to client:9000.  
 2\. Browser sends the `XDEBUG_SESSION_START` parameter to server:80.  
 3\. The webserver detects the parameter and starts the Xdebug server.  
 4\. Xdebug server sends a message to server:9000.  
 5\. SSH captures the message on server:9000 and forwards it to client:9000.  
 6\. DBGP client picks up the message on client:9000 and processes it.  
 7\. DBGP client sends a response back to client:9000.  
 8\. SSH captures the message on client:9000 and forwards it to server:9000.  
 9\. Xdebug server picks up the message on server:9000 and processes it.

* * *

###Conclusion###  
So Xdebug is pretty awesome! It can turn Notepad++ into a fairly powerful debugging engine, complete with breakpoints, and variable peeking and watching. Unfortunately, so far, I've only figured out how to configure it with one developer per server. I bet if you manipulate the ports, you can get multiple developers on one server or multiple servers for one developer. Once I get through that, I'll write a follow up. Stay Tuned!

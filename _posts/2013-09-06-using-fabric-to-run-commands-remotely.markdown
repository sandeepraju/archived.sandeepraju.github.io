---
layout: post
title: "Using Fabric To Run Commands Remotely"
date: 2013-09-06 05:53:00
comments: true
categories: [tech, dev, python]
published: true
---


At times, you might have to do some _ninjaing_ around to run commands on mutiple servers. If you are like me, a semi literate in [shell scripting](http://en.wikipedia.org/wiki/Shell_script), you'd google for the shell / [awk](http://en.wikipedia.org/wiki/AWK) syntax and waste hours writing scripts to do this. But then, wasting hours to run a simple script on multiple servers is pointless. What if I can do this with Python? All problems solved :) That is where [Fabric](http://docs.fabfile.org/en/1.7/) comes into picture!

<!-- more -->

>Fabric is a Python library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks.

Installing fabric is easy. Just run the following commands to install fabric via [pip](https://pypi.python.org/pypi/pip).

{% highlight bash %}
apt-get install gcc python-dev python-setuptools  
pip install fabric
{% endhighlight %}

This enables the _fab_ command which will be used to run fabric scripts. Fabric script file is named `fabfile.py` by convention. So, let us create a small script to check how many users logged into each server.

{% highlight python %}
# filename: fabfile.py
from fabric.api import env, run

env.hosts = ['192.168.0.9', '192.168.0.10', '192.168.0.11']

def logged_in_users():
    run('who')
{% endhighlight %}

Now, when we run this function using fabric, an ssh connection is made into each of the servers listed and the function is run. We can run this using the following command,

{% highlight bash %}
fab logged_in_users
{% endhighlight %}

Note that due to the naming convention we followed, we need not even give the filename to the fab command. It will automatically look for the file named `fabfile.py`    and runs the function you have specified.  

Here is what you get as output, the logged in users of each system,


    [192.168.0.9] Executing task 'logged_in_users'
    [192.168.0.9] run: who
    [192.168.0.9] out: sandeep  tty7         2013-09-05 15:24 (:0)
    [192.168.0.9] out: alex     pts/0        2013-09-05 22:38 (:0)
    [192.168.0.9] out: root     pts/2        2013-09-06 03:31 (192.168.0.9)
    [192.168.0.9] out:

    [192.168.0.10] Executing task 'logged_in_users'
    [192.168.0.10] run: who
    [192.168.0.10] out: sandeep  tty7         2013-09-05 15:22 (:0)
    [192.168.0.10] out:

    [192.168.0.11] Executing task 'logged_in_users'
    [192.168.0.11] run: who
    [192.168.0.11] out: sandeep  tty7         2013-09-05 15:21 (:0)
    [192.168.0.11] out:


    Done.
    Disconnecting from 192.168.0.11... done.
    Disconnecting from 192.168.0.10... done.
    Disconnecting from 192.168.0.09... done.


If you just ran this command, you'd realise that the process happens in a sequence. That is, the command is run on each server one after another. To run this command parallely on the listed server, pass the -P flag to the command. So, the same command runs the function in parallelly on each server,

{% highlight bash %}
fab -P logged_in_users
{% endhighlight %}

Now, with fabric, running scripts on multiple servers is simple and quick! :)  



__SOURCE:__ This post is based on [A Brief Introduction to Fabric](https://gist.github.com/DavidWittman/1886632) which I stumbled upon when I was looking for getting started with Fabric quickly. Check it out. Its pretty cool :)

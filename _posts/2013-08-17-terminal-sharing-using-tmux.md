---
layout: post
title: "Terminal Sharing Using Tmux"
date: 2013-08-17 22:10:00
comments: true
categories: [tech, linux, dev]
published: true
---

Recently, I helped my friend setup [his octopress blog](http://vijaym.in). The problem was that, we were miles apart and interacting via chat turned out to be hard. What we needed was a shared terminal where both of us could see the commands entered by either of us. A quick google search showed me that this was possible easily using [tmux](http://tmux.sourceforge.net/).

<!-- more -->

Simply said, tmux is a _terminal multiplexer_. What does that means? Here is what the [official site](http://tmux.sourceforge.net/) says...

>It lets you switch easily between several programs in one terminal, detach them (they keep running in the background) and reattach them to a different terminal. And do a lot more.

Basically, it's a better alternative to [screen](http://www.gnu.org/software/screen/) which I was already using for a while.

This is what I did to share _his_ terminal with _me_,

* We installed tmux using the command `sudo apt-get install tmux` on both the systems.
* Asked him to run the following commands on the terminal that _he_ wants to share with _me_.

{% highlight bash %}
# specify the name of the tmux socket with -S when creating it
tmux -S /tmp/share

# chmod to allow me to access his socket
chmod 777 /tmp/share
{% endhighlight %}

* Then, I logged into _his_ system via SSH and ran the following command to access the terminal _he_ is sharing with _me_.

{% highlight bash %}
# now to connect to his shared terminal
tmux -S /tmp/share attach
{% endhighlight %}

As simple as that!

Happy coding :)

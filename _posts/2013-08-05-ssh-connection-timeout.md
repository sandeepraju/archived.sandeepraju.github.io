---
layout: post
title: "SSH Connection Timeout"
date: 2013-08-05 00:00:00
comments: true
categories: [tech, dev]
published: true
---

A part of my work involves ssh-ing to multiple servers and working remotely. One of the major problems I faced with this was the frequent ssh timeout and as a result I had to close my hung terminal and re-ssh back. Recently I got fed up of this and found a solution to this problem on [a superuser thread](http://superuser.com/questions/98562/way-to-avoid-ssh-connection-timeout-freezing-of-terminal-tab).

<!-- more -->

The solution involved configuring ssh on either the client machine or on the server. The basic reason why ssh times out is because the ssh server expects a _sign-of-life_ signal from your client at regular intervals.

Add the following configuration on the `~/.ssh/config` file in you client(local) system.


	Host remotehost
	  HostName remotehost.com
	  ServerAliveInterval 60


this tells ssh to send _sign-of-life_ signal every 60s whenever you are connected to remotehost.com. You can also enable it for all hosts that you connect to by changing the remotehost to * as follows.


	Host *
	  ServerAliveInterval 60


__UPDATE:__ Nevertheless, sometimes, due to some network issues the session might hang. In such cases, to end the SSH session, press `ENTER ~ .` in sequence.

Happy coding! :)

---
layout: post
title: "SHARQ - an Open Source, Rate Limited, Queueing System"
date: 2014-10-20 00:00
comments: true
categories: [tech, dev, opensource]
published: true
---

I have been fascinated about task queues since my college days. The way they are designed & work has always interested me. Being a Pythonista, I have been using [Celery](http://www.celeryproject.org/) for a long time. [Ask](https://twitter.com/asksol) has done an amazing job building Celery and the ecosystem around it. It works great for general use-cases faced by a lot of companies in their day to day workflows (like sending out emails in the background, running periodic tasks, so on). However, sometimes that doesn't suffice.

Recently, I faced an unique situation. I had to rate limit the queues in a specific system. Having used Celery for quite a few years, it was my natural choice. But soon, I realized that creating queues dynamically is not possible with Celery. Celery also lacked a clean way to [update the rate limit of a queue globally](http://celery.readthedocs.org/en/latest/userguide/tasks.html#Task.rate_limit). This was not acceptable for my use case.

All my efforts at searching for a queue with this specific feature set (dynamic queues, realtime rate limits & constant flow rate) resulted in scripts and hacks written by several people, around various existing systems that were not built for this use case. It was apparent after some time that there was no simple, out of the box, cleaner solution for this problem and there was a scope for filling this void. This is how SHARQ came into existence.

SHARQ bridges this void in a simple and elegant manner by supporting all the above mentioned features. It is built using [Python](http://www.python.org/) ([Flask](http://flask.pocoo.org/) & [Gevent](http://www.gevent.org/) and uses [Redis](http://redis.io/) as a broker. It talks HTTP with JSON payloads which makes writing workers in any language easy.

SHARQ can be installed from [PyPI](https://pypi.python.org/pypi) using the following command:

{% highlight bash %}
pip install sharq-server
{% endhighlight %}

Once SHARQ is installed, it will expose a sharq-server command. The command is minimal and accepts a SHARQ configuration file. To get started quickly, grab the SHARQ [sample configuration file from here](https://raw.githubusercontent.com/plivo/sharq-server/master/sharq.conf). The SHARQ Server can be started with the following command:

{% highlight bash %}
sharq-server --config sharq.conf
{% endhighlight %}

and you'll see SHARQ server running in foreground:

	___ _              ___    ___
	/ __| |_  __ _ _ _ / _ \  / __| ___ _ ___ _____ _ _
	\__ \ ' \/ _` | '_| (_) | \__ \/ -_) '_\ V / -_) '_|
	|___/_||_\__,_|_|  \__\_\ |___/\___|_|  \_/\___|_|

	Version: 0.1.0

	Listening on: 127.0.0.1:8080

Now, you can play around with SHARQ using its [REST APIs](http://sharq.io/docs/apireference.html).To know more about how to use SHARQ and its enqueue-dequeue-finish workflow, checkout the [getting started section on sharq.io](http://sharq.io/docs/gettingstarted.html).

SHARQ is open source and is [available on Github](https://github.com/plivo/sharq-server). Pull requests are always welcome!

## Further Reading

* [The SHARQ docs](http://sharq.io/docs).
* [SHARQ at PyCon India 2014](https://speakerdeck.com/sandeepraju/sharq-an-open-source-rate-limited-queueing-system-pycon-india-2014).
* [SHARQ resources of the PyCon talk](https://github.com/sandeepraju/PyCon-India-2014).
* SHARQ example [Enqueue](https://gist.github.com/sandeepraju/bfa72c7027e1d739b33e) and [Dequeue](https://gist.github.com/sandeepraju/3da0ad035aa9bf5504b1) gists.
* [SHARQ on Plivo's blog](https://www.plivo.com/blog/sharq-a-flexible-open-source-rate-limited-queuing-system/).


__NOTE__: I am the primary author of SHARQ and will be maintaining it constantly with features and bug fixes. SHARQ was written at [Plivo Inc](https://www.plivo.com). All the views and opinions expressed in this article are my own and does in no way reflect that of Plivo or any other entity.

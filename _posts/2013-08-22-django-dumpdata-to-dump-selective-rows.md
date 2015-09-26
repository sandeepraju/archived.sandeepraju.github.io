---
layout: post
title: "Django Dumpdata To Dump Selective Rows"
date: 2013-08-22 08:14
comments: true
categories: [dev, django, web, python]
published: true
---

Thanks to my [telephony geek friend](https://twitter.com/GurteshwarSingh) for letting me know this simple but highly useful method to selectively dump [django](https://www.djangoproject.com) models data into a django standard _model-dump_ format. Here is a simple snippet which shows how to selectively dump only the required rows and not the entire model.

<!-- more -->

{% highlight python linenos %}
from django.core import serializers

# Generate a queryset depending on whatever you need
query_set = YourModel.objects.all()[:2000]  # selecting only first 2k rows

# Serialize the queryset into JSON with indentation of 4 spaces
json_data = serializers.serialize("json", query_set, indent=4)

# Dump the json data into a file
output_file = open('YourModel.json', 'w')
output_file.write(json_data)
output_file.close()
{% endhighlight %}

Works like a charm!

Happy coding :)

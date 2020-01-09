---
layout: post
title:  "URL Shortener"
date:   2020-01-05 15:07:19
categories: [Python]
comments: true
---
I wanted to play around with Python, so I created a simple URL shortener using the 'pyshorteners' library and accessing the 'Shortener' class. 

A URL shortener is useful when dealing with long links.

{% highlight python %}
from pyshorteners import Shortener

class Shortening:
    shortener = Shortener('Tinyurl')

    def shortenURL(self):
        self.url = input("Enter the URL you would like to shorten: ")
        shortURL = self.shortener.short(self.url)
        print("Your new URL is: " + shortURL)

    def decodeURL(self):
        self.url = input("Enter the URL you would like to expand: ")
        longURL = self.shortener.expand(self.url)
        print("Your new URL is: " + longURL)

app = Shortening()

app.shortenURL()
{% endhighlight %}
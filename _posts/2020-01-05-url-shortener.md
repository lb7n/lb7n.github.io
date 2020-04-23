---
layout: post
title:  "URL Shortener"
date:   2020-01-05 15:07:19
categories: [Python]
comments: true
---
This is a very basic URL shortener program that utilizes the pyshorteners Python library to access TinyUrl from your terminal.

A URL shortener is useful when dealing with long links, and the pyshorteners API wrapper is a really simple way to do that. Another cool feature of utilizing Shortener is that we can also expand URL's that were previously shortened.

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
---
layout: post
title: Getting Started With The Asset Pipleline
date: 2016-06-24 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, Ruby, Ruby on Rails, Asset Pipeline, Sass, CSS, JS, JavaScript]
---

{% img /images/Asset-Pipeline/Siphon_Lock_Overview.png %}

## Topics

+ Test Speed

Concatentate, minify, compress
JS, CSS assets

Write in other languages like CoffeeScript, Slim, Haml, Sass

Sprockts Extraction
Not a core functionality since Rails 4

Enabled by default though

Skip pipeline when you initiate project

Rails 4 makes a couple of decisions for you though. It adds gems for Sass, CoffeeScript and compression

Setting asset compression methods

## Why Asset Pipeline?

### Concatenation & Compression?

Reduce number of requests for rendering pages

Faster loading of your apps

Sprockets concatentates, it collects all your CSS and JS files into master files. So you end up with only one file for each which means only one request for each CSS and JS files—instead of one request for each of your possibly myriad of asset files.

Minification is cool because it gets rid of unnecessary stuff in your files. Whitespace and comments basically. This is not made for human readability but for machine consumption. The files will end up looking cryptic and hard to read but its still all valid CSS and JS code—just without any excess whitespace to make it readable and understandable for humans. Web browsers don’t need that formating though and that let’s us optimize these assets for downloading and rendering.

### Higher-Level Languages

Sass
CoffeeScript
Haml
Slim

We can write in languages that can be more convenient and efficient to work in and then precompile their produced assets into CSS and JS before they are deployed and rendered by the browser.




### MD5 Fingerprint?

Rails creates a fingerprint for each filename. ?? Example fingerprint filename 

That way it can easily determine if a file has changed its contents and can update the file. If nothing has changed it is cached by the web browser for future requests.

When you change the contents, a new fingerprint will created and added to the filename of your asset.

So the filename changes with changes to the contents of the file. That way Rails can easily compare versions of the same file and decide if it needs let the browser request a new version for it.

The added hash is a mathematical function that converts the contents of a file into a unique sequence of 32 hexadecimal digits

That means that you get the same hashed result when you apply the function to the same content twice—or how often you like.

``` bash

MD5("The quick brown fox jumps over the lazy dog")

```

``` bash

9e107d9d372bb6826bd81d3542a419d6

```



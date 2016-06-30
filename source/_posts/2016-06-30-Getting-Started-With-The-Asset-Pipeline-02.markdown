---
layout: post
title: Getting Started With The Asset Pipleline 02
date: 2016-06-30 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, Ruby, Ruby on Rails, Asset Pipeline, Sass, CSS, JS, JavaScript]
---

{% img /images/Asset-Pipeline/Siphon_Lock_Overview.png %}

## Topics

+ Using The Pipeline

## Using The Pipeline

The Asset Piple line was extracted since Rails 4 and is not a core functionality anymore. Sprockets is now in charge of it. It is enabled by default though.

Enabled by default though

Skip pipeline when you initiate project

Rails 4 makes a couple of decisions for you though. It adds gems for Sass, CoffeeScript and compression

Setting asset compression methods


Your CSS, JS, CoffeeScript, Sass, Slim, etc files are now organized in one central and convenient place, under `app/assets`.

The Sprockts middleware??? is reponsible for serving files in this directory—Sprockets is in charge. `app/assets` is the place to put files that still need preprocessing, like turning Sass files into their equivalent CSS files.

Also, files placed in `app/assets` get precompiled automatically for going into production—before being deployed onto a server.That means that the files in your `assets` folder are never served as-is for production—they are expected to be processed first.

?? require_tree

### image_tag

Images placed in `public/assets/images` directory you can use a convenience method to access images without fiddling around with path names yourself.

###### some.html.erb file

``` ruby

<%= image_tag "some-image.png" %>

```

If activated, Sprockets will serve such files if found. When a file like `some-image.png` is fingerprinted like `some-image-9e107d9d372bb6826bd81d3542a419d6.png it will be treated the same way.

If you need other directories within `public/assets/images` or within `app/assets/images` to organize your images, something extra for icons or svg files maybe Rails will have no problem finding them but you need to add the directory’s name first:

``` ruby

<%= image_tag "svgs/some.svg" %>

<%= image_tag "icons/some-icon.png" %>

```

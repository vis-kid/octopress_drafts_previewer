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

+ Asset Pipeline?
+ Organization
+ Using The Pipeline

## Asset Pipeline?

What does the Asset Pipeline have to offer? Despite the fancy name, it’s relatively easy to break down what it can do for you. The main thing it takes care for you is concatenation, minification and preprocessing of assets that are written in higher-level languages like CoffeeScript or Sass for example. That can bring not only a boost in quality and speed but also in convenience. In this regard we could talk about convenience over configuration.

The other thing that should not be underestimated is organization. The pipleline offers a solid frame to place your assets. On smaller projects this might not seem that important, sure, but bigger projects might not recover easily from going in the wrong direction on subjects like this. Sure it’s not the best example in the world, but just imagine a project like Facebook or Twitter having shitty organized assets for their CSS and JS. Not that hard to imagine that this would breed trouble, big time, and that peeps who have to work and build on such a basis have not an easy time to love their jobs.

As with many things in Rails, there is a conventional approach of how to handle assets. This makes it easier to onboard new people and to avoid having developers and designers being too obsessed with bringing their own dough to the party. When you join a neww team, you want to be able to get up to speed quickly and hit the the ground running. Needing to figure out the special sauce of how other peeps organized their assets is not exactly helpful with that. In extreme cases this can even be regarded wasteful or even burning money unnecessarily—but let’s not get too dramatic here.

The Asset Pipeline is not exactly news to people in the business but for beginners it can be a little tricky to figure it out right away. Developers don’t exactly spend a ton of time on front-end stuff, especially when they start out they are busy with lots of moving parts that have nothing to do with HTML, CSS or the often bashed JS bits—no offense JS, I got no beef with you these days.

So this article is for the beginners among you and I recommend taking a look at the pipeline right away and get a good grip on this topic right away—even if you won’t expect to work much on markup or front-endy things much in the future. It’s kind of an essential aspect in Rails and not that huge of a deal to figure out quickly. Plus your team members, especially designers who spend half their lives in these directories will put less funny stuff in your coffee if you have some common ground where you meet half way.

### Concatenation & Compression?

Why do we need that stuff? Long story short, you want your apps to load faster. Period! Anything that helps with that is welcome. Well, of course you can get away without optmizing for that but it has too many advantages that one simply can’t ignore. Compressing file sizes and concatenating multiple asset files into one “master” file that is downloaded by a client like a browser is also not that much work. It is mostly handled by the pipleline anyway.

Reducing the number of requests for rendering pages is very efffective at speeding up the rendering of pages. Instead of having maybe 10, 20, 50 or whatever number of requests before the browser is finished getting your assets you could potentially only have one for each CSS and JS assets. That is not only nice, this was a game changer at some point. These kinds of improvements in web development should not be neglected because we are dealing with miliseconds as a unit. Lots of requests can add up pretty substantially. And after all, the user experience is what matters most for successful projects. Having users who need to wait just a little bit extra before they can see content on your page can be a massive dealbreaker.

Minification is cool because it gets rid of unnecessary stuff in your files. Whitespace and comments basically. These compressed files are not made with human readability in mind but for machine consumption. The files will end up looking a bit cryptic and hard to read but its still all valid CSS and JS code—just without any excess whitespace to make it readable and understandable for humans. The JS compression is a bit more involved though. Web browsers don’t need that formating and that let’s us optimize these assets for downloading and rendering web pages and their behaviour.

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



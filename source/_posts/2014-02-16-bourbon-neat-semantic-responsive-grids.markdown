---
layout: post
title: "Bourbon Neat: Semantically Responsible Responsive Grids"
date: 2015-09-09 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [ Sass,  Bourbon, Neat, Thoughtbot, SCSS, CSS, Design ]
---

{% img /images/bourbon-neat/bourbon-neat.jpg 450 260  %}

[{% img /images/bourbon-neat/Bourbon-Neat-Logo.png  250 450 %}](http://neat.bourbon.io/)

## Welcome to your new favorite tool

Bourbon Neat was conceived by [Reda Lemeden](https://twitter.com/kaishin) and is part of the fantastic [Bourbon](http://bourbon.io/) suite. Its basically a lightweight responsive grid framework that was built on top of Sass and Bourbon. If semantic markup is your thing and presentation classes make you mad, this smart library will brighten your day. Btw, the project is supported by [thoughtbot](https://thoughtbot.com/) and their designers take care of it.

Before we install the thing, let me give you a couple of good reasons to look into it. We all know countless frameworks that promise to deliver a stable frame for developing your designs. And certainly most succeed in that regard in one way or another. The relevant questions are in how many regards and to what degree:

+ Will you be able to switch frameworks in the future easily?
+ Problems with cluttering your markup?
+ Are they suited for scaling projects?
+ Are they ridiculous in size?
+ Steep learning curve?
+ Generic look?

If any of the issues above are remotly ringing a bell, I just want to assure you that such headaches are easily avoidable these days. Its very rare to come across a project that balances its payoffs so well as *Bourbon Neat* (Or just Neat for short). Why so? Here are a couple of good reasons:

+ super lightweight
+ future-proof
+ easy to use
+ responsive
+ semantic
+ scalable
+ elegant

Bourbon also aims to provide you with options—not answers. What I like especially is that it isn’t a factory for generic, pre-defined styles. That way it aids the designer’s own design decisions without standing in the way. 

## A word about semantics

This is an important issue but I’ll make it short:
These ugly *presentation classes* and additional *“empty” wrapper divs* that you often find in similar libraries are ghosts from the past—at least they should be ghosted really. *Neat* plays a significant role in moving past these gnarly semantic habits. Nowadays you can easily write *clean, unobtrusive markup* and have all your grid styles cleanly separated in your stylesheets by including mixins. Busted!

{% img /images/bourbon-neat/Ghostbusters.gif 350 %}

## Neat Grids

Why deal with a grid framework at all? Well, design is all about relationships and relationships are obviously hard. Grids make them easier by helping you tie structures together more meaningfull and organized.

What I like most about grids is that they *simplify, reduce* and *stabilize*—in essence, they help trim the fat out of designs. Unless applied mindlessly of course, that’s exactly what Neat helps you achieve so effortlessly. Neat’s grid framework offers a lot of power in a small looking package that is easy to use. Hard to believe this library consists of only:

+ ### 14 mixins

  + reset-layout-direction
  + direction-context
  + shift-in-context
  + display-context
  + outer-container
  + span-columns
  + reset-display
  + fill-parent
  + reset-all
  + omega
  + media
  + shift
  + pad
  + row

+ ### one function for setting breakpoints 

  + new-breakpoint

+ ### and twelve variables for settings 

  + default-layout-direction
  + visual-grid-opacity
  + border-box-sizing
  + visual-grid-index
  + disable-warnings
  + visual-grid-color
  + default-feature
  + grids-columns
  + max-width
  + visual-grid
  + column
  + gutter

Boom, that’s it! Pretty low key but it equips you with a lot of horsepower!

## Responsive Grids

{% img /images/bourbon-neat/grids.gif 750 %}

Its becoming obvious that *change* and the *need for flexibility* are constant future-proof necessities. Incorporating media queries should be smooth and easily manageable but they can quickly become a mess if not handled with care. 

Bourbon Neat encourages a [*DRY*](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) approach for handling your breakpoints with its **new-breakpoint** function. Save breakpoints in variables and resuse them wherever you need. Changing a bunch of media queries in one place is hard to beat. 

To be more concrete, let me give you one little teaser.
Here’s some random Sass example using the **new-breakpoint** function for media-queries:
``` sass
$mobile: new-breakpoint(max-width 500px 4)

.sidebar
  +span-columns(3)
  +media($mobile)
	  +span-columns(1)

.content
  +span-columns(9)
	+media($mobile)
	  +span-columns(3)
```
In my next article I’ll dive deeper into the nitty-gritties of this but what should be apparent to you in this example is the ease of use if you decide to change your media queries. Through the use of Sass variables in this function, you’ll have one consistent, authoritative place to change / tweak your responsive layout without touching each element individually. 

## Installation

Now that you know what you’re getting yourself into, let’s install this beauty:
(You need [Sass](http://sass-lang.com/install) installed before you start of course)

+ #### #01 Install Bourbon

Take a look at my article about Bourbon if you need to follow this step.

+ #### #02 Install the Neat gem via [RubyGems](https://rubygems.org/)

``` 
gem install neat
```

+ #### #03 Install Neat

Change into a Sass directory of your choosing:

```
neat install
```
This will install all the necessary mixins, settings and functions in your designated directory.

### Screenshot:
{% img /images/bourbon-neat/neat_directories.png %}

+ #### #04 Do a Sass import for Neat in your Sass stylesheet

``` sass
@import 'bourbon/bourbon'
@import 'grid-settings'
@import 'neat/neat'
```

As you can see, the order is important here. Because Neat was built on top of Bourbon you need to import Bourbon first. Same goes for **grid-settings**.

## Rails

If you want to use Neat with Rails you’ll just add

+ #### #01

``` ruby
gem neat
``` 

to your Gemfile and run

+ #### #02

``` bash
bundle install
```

in your terminal.

+ #### #03

In **application.sass** you’ll simply add

```sass
@import 'bourbon'
@import 'grid-settings'
@import 'neat'
```
and you’re good to go. You should remember though that `@import` isn’t playing well with Sprockets directives. Therefore you need to delete Sprockets directives. In **application.sass**, all references to **require**, **require_tree**, and **require_self** need to go.

Last but not least, the fine folks at thoughtbot provided you with a nice command line interface. It comes with three self-explanatory commands:

```bash
neat install

neat update

neat remove
```

## Closing $0.02

Given that you’re using intelligent tools, its definitely a joy working with grids. This framework does a great job in planning for developer happiness—long- and short-term. To me there is but one word which describes best what Bourbon Neat has provided me with when I work with grids: *Zen*. Hard to find better developer / designer happiness than that imho. 

In the next article I’ll take a closer, more technical look at how to use this fantastic gem.
Have fun playing with Neat! I know you will!

{% img /images/bourbon-neat/ping-pong-grids.gif 450 %}

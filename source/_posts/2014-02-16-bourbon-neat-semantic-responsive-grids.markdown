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
We all know the myriad of frameworks that promise to deliver a stable frame for developing your designs. And certainly most succeed in that regard in one way or another. The relevant questions are in how many regards and to what degree:

+ Will you be able to switch frameworks in the future easily?
+ Problems with cluttering your markup?
+ Are they suited for scaling projects?
+ Are they ridiculous in size?
+ Steep learning curve?
+ Generic look?

If any of the issues above are remotly ringing a bell, I just want to assure you that such headaches are easily avoidable these days. It’s very rare to come across a project that balances it’s payoffs so well as *Bourbon Neat*. Why so? Here are a couple of good reasons:

+ super lightweight
+ future-proof
+ easy to use
+ responsive
+ semantic
+ scalable
+ elegant

Also it aims to provide you with options—not answers. What I like especially is that it isn’t a factory for generic, pre-defined styles. That way it aids the designer’s own design decisions without standing in the way. 

## A word about semantics

This is an important issue but I’ll make it short:
These ugly *presentation classes* and additional *“empty” wrapper divs* that you often find in similar libraries are ghosts from the past—at least they should be ghosted really. *Neat* plays a significant role in moving past these gnarly semantic habits. Nowadays you can easily write *clean, unobtrusive markup* and have all your grid styles cleanly separated in your stylesheets by including mixins. Busted!

{% img /images/bourbon-neat/Ghostbusters.gif 350 %}

## Neat Grids

Why deal with a grid framework at all? Well, design is all about relationships and relationships are hard. Grids make them easier by helping you tie structures together more meaningfully and organized.

What I like most about grids is that they *simplify, reduce* and *stabilize*—in essence, they help trim the fat out of designs. Unless applied mindlessly of course, that’s exactly what Bourbon Neat helps achieve so effortlessly. Neat’s grids offer a lot of power in a small looking package that is easy to use. Hard to belive this library consists of only:

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
  + visual-grid-color
  + disable-warnings
  + default-feature
  + grids-columns
  + max-width
  + visual-grid
  + column
  + gutter

Boom! That’s it!

## Responsive Grids

{% img /images/bourbon-neat/grids.gif 750 %}

It’s becoming obvious that *change* and the *need for flexibility* are constant future-proof necessities. Incorporating media queries should be smooth and easily manageable but they can quickly become a mess if not handled with care. 

Bourbon Neat encourages a [*DRY*](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) approach for handling your breakpoints with it’s **new-breakpoint** function. Save breakpoints in variables and resuse them wherever you need. Changing a bunch of media queries in one place is hard to beat. 

Given that you’re using intelligent tools, it’s definitely a joy working with grids. This framework does a great job in planning for developer happiness—long- and short-term. To me there is but one word which describes best what Bourbon Neat has provided me with when I work with grids: *Zen*. Hard to find better developer / designer happiness than that imho. 

In the next article I’ll take a closer, more technical look at how to use this fantastic gem.
Have fun playing with Neat, I know you will!

{% img /images/bourbon-neat/ping-pong-grids.gif 450 %}

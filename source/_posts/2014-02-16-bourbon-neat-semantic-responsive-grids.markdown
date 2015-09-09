---
layout: post
title: "Bourbon Neat - Semantic Responsive Grids"
date: 2014-02-16 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [ Sass,  Bourbon, Neat, Thoughtbot, SCSS, CSS, Design ]
---

{% img /images/bourbon-neat/bourbon-neat.jpg 450 260  %}

[{% img /images/bourbon-neat/Bourbon-Neat-Logo.png  250 450 %}](http://neat.bourbon.io/)

[ -- **Context: Rails 4 apps** -- ] 

[ -- **Requirements: Sass 3.2+** -- ]

[ -- **Requirements: Bourbon 2.1+** -- ]

## Welcome to your new favorite tool
We all know the myriad of frameworks that promise to deliver a stable frame for developing your designs. And certainly most succeed in that regard in one way or another. The relevant questions are in how many regards and to what degree:

+ ** Are you be able to switch frameworks in the future easily?**
+ **Are they suited for scaling bigger projects?**
+ **Problems with cluttering your markup?**
+ **Are they HUGE — I mean way too big?**
+ **Steep learning curve?**
+ **Generic look?**

If any of the issues above are remotly ringing a bell, I just want to assure you that such headaches are easily avoidable these days. It's very rare to come across a project that balances it's payoffs so well like **Neat**. <br> Why? How is it so different?

<!-- more -->

### By being

+ **super lightweight** 
+ **future-proof**
+ **responsive**
+ **easy to use**
+ **scalable**
+ **semantic**
+ **elegant**
+ **clean**

… and by providing you with options — not answers or by being a factory for generic design decisions. 

## Semantics
I will make it short:
**Presentation classes** and additional **“empty” divs** as wrappers are ghosts from the past — at least they should be ghosted really. **Neat** plays a significant role in moving past those gnarly semantic habits. You can write **clean, unobtrusive markup** and have all your grid styles cleanly separated in your stylesheets by including mixins. Busted!

{% img /images/bourbon-neat/Ghostbusters.gif 350 %}

## Grids

Design is all about relationships and relationships are hard. Grids make them easier by helping to tie structures together more meaningfully.

What I like most about grids is that they **simplify, reduce** and **stabilize** – in essence, they help trim the fat out of designs — if not applied mindlessly at least. That's exactly what Bourbon Neat helps achieve so effortlessly. 

It's a lot of power in a small looking box — and easy to use as well. **Neat** consists of “only” 

+ ### **eleven mixins**
  + outer-container
  + span-columns
  + reset-display
  + reset-layout
  + fill-parent
  + reset-all
  + omega
  + media
  + shift
  + pad
  + row

##  

+ ### one **function** for setting **breakpoints** 
  + new-breakpoint

##  

+ ### **eleven variables for settings** 
  + default-layout-direction
  + visual-grid-opacity
  + border-box-sizing
  + visual-grid-index
  + visual-grid-color
  + default-feature
  + grids-columns
  + max-width
  + visual-grid
  + column
  + gutter



## Responsive Grids

{% blockquote %}

“The taller the bamboo grows, the lower it bends”

{% endblockquote %}

{% img /images/bourbon-neat/Responsive-Bamboo.png 750 %}

It's becoming obvious that **change** and the **need for flexibility** are constant future-proof necessities. Incorporating media queries should be smooth and easily manageable but they can quickly become a mess if not handled with care. 

Bourbon Neat encourages a **DRY approach** for handling your breakpoints with it's **new-breakpoint function**. Save breakpoints in variables and resuse them wherever you need. Changing a bunch of media queries in one place is hard to beat. 

It's definitely a joy working with grids — given that you're using the right tools. This framework does a great job planning for developer happiness — long- and short-term. To me there is but one word which describes it best: **ZEN**. 

In the next article I will take a closer, more technical look at how to use this fantastic gem.
Have fun playing with Neat — I know you will!

{% img /images/bourbon-neat/ping-pong-grids.gif 450 %}

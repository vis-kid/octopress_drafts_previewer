---
layout: post
title: Neat Basics
date: 2015-09-15 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [ Sass,  Bourbon, Neat, Thoughtbot, SCSS, CSS, Design ]
---

{% img /images/Neat_01/Neat-pour.jpg %}

[{% img /images/bourbon-neat/Bourbon-Neat-Logo.png  250 450 %}](http://neat.bourbon.io/)

I assume you have taken a look at my previous intro article about Neat. So without further ado, let me pour you your first sip of Bourbon Neat. 

## Neat Basics #01

In this piece I’ll take a newbie-friendly look at the following function, mixins and variables:

#### Function
+ new-breakpoint

#### Mixins

+ shift-in-context()
+ outer-container
+ span-columns()
+ media()
+ omega
+ shift()
+ pad()

#### Variables

+ $visual-grid-opacity
+ $visual-grid-index
+ $visual-grid-color
+ $grid-columns
+ $visual-grid
+ $max-width

## Mixins

+ ### outer-container

In your **grid-settings** file you can specify a **$max-width** Sass variable that defines the maximum width that the content of your page should span. For example, Neat comes with a easily changeable **$max-width** setting of 1088px (converted to em) out of the box. That setting basically specifies the width of your outermost container for your “main” content. 

When you make a container the outer-container, Neat automatically centers it in the viewport (by adding **margin-left: auto** / **margin-right: auto**), clears the floats and applies the specified **$max-width**. It is an optional mixin (recommended though) and you can have multiple outer container elements on a single page. The one thing you can’t do is nest them. 

The outer container holds your layout. Within its element you’ll build your grid which can span as many columns across as specified in your settings under **$grid-columns** (default to 12 columns). All the elements in a row have to add up to the total number of columns specified. 

In the dummy example below, you’ll see that the **container** element wraps a couple of **aside** and **article** tags. They span 3 and 9 columns respectively and simply add up to 12 columns as specified in my settings. If I’d go over that number of columns the layout would certainly break. Think of the **outer-container** mixin as the most likey prerequisite for adding grid layouts within container elements.

Haml:

``` haml
.container
  %aside Aside
  %article Article
  %aside Aside
  %article Article
  %aside Aside
  %article Article
```

Sass:

``` sass

body
  background-color: white

.container
  +outer-container
  background-color: tomato

aside
  +span-columns(3)
  margin-bottom: 5px
  height: 200px
  background-color: LightSkyBlue 

article
  +span-columns(9)
  +omega
  margin-bottom: 5px
  height: 200px
  background-color: Olive
```

You don’t need to concern yourself too much about **span-columns** and **omega** in this example for now. After a couple of paragraphs it will be crystal clear to you.


#### Screenshot

{% img /images/Neat_01/outer-container.png %}

Here is also a codepen for playing around with it:
http://codepen.io/vis-kid/pen/xwZJOP

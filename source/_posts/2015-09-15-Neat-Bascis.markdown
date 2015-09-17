---
layout: post
title: Bourbon Neat Basics
date: 2015-09-15 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [ Sass,  Bourbon, Neat, Thoughtbot, SCSS, CSS, Design ]
---

{% img /images/Neat_01/Neat-pour.jpg %}

[{% img /images/bourbon-neat/Bourbon-Neat-Logo.png  250 450 %}](http://neat.bourbon.io/)

I assume you have taken a look at my previous intro article about Neat. So without further ado, let me pour you your first sip of Bourbon Neat. In this piece I’ll take a newbie-friendly look at the following function, mixins and variables:

#### Function
+ new-breakpoint

#### Mixins

+ shift-in-context
+ outer-container
+ span-columns
+ media
+ omega
+ shift
+ pad

#### Variables

+ $visual-grid-opacity
+ $visual-grid-index
+ $visual-grid-color
+ $grid-columns
+ $max-width
+ $visual-grid

## Mixins

+ ### outer-container

When you make an element the outer container, Neat automatically centers it in the viewport (by adding **margin-left: auto** / **margin-right: auto**), clears the floats and applies the specified **$max-width**. It is an optional mixin (recommended though) and you can have multiple outer container elements on a single page. The one thing you can’t do is nest them. 

The outer container holds your layout grid. Within it, your grid can span as many columns across as specified in your **grid-settings** file via the **$grid-columns** variable (default to 12 columns). That means all the elements in a row have to add up to the total number of columns specified there. 


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

The background colors are supposed to make it easier to see how the pieces fit together. You don’t need to concern yourself too much about the **span-columns** and **omega** mixins in this example for now. After a couple of paragraphs they will be crystal clear to you.

#### Screenshot

{% img /images/Neat_01/outer-container.png %}

Here is also a codepen for playing around with it:
http://codepen.io/vis-kid/pen/xwZJOP

In your settings file you can also specify a **$max-width** Sass variable that defines the maximum width that the content of your page should span. For example, Neat comes with a easily changeable **$max-width** setting of 1088px (converted to em) out of the box. That setting basically specifies the width of your outer container elements.

There is also the option to provide this mixin with an argument for a **$local-max-width** if you want a certain container element to have a different **max-width** than the global one set in **grid-settings**. This overwrites locally the default **max-width** from your settings file. You can provide *pixel*, *em* or *percentage* arguments. The columns of your grid inside that container adjust their width automatically but the number of columns stays the same.

```sass
.container
  +outer-container(800px)
  background-color: tomato
```
```sass
.container
  +outer-container(80%)
  background-color: tomato
```

http://codepen.io/vis-kid/pen/vNGOqj

+ ### span-columns

Just in case some of you are new to designing with grids, you should maybe look into this excellent book by [Khoi Vinh](http://www.subtraction.com/2010/11/05/i-wrote-a-book/). I highly recommend it. One concept that you need to understand right away though is that you build up your grid designs through a series of columns that span across the page. 

The basic functionality of this is super straightforward in this framework. You just pick an element and tell it how many columns it should span within the total number of **$grid-columns**. Let me demonstrate the basics. 

Haml:

``` haml
.container
  %aside.first  First: 2 columns
  %article.second  Second: 10 columns

  %aside.third  Third: 4 columns
  %article.fourth  Fourth: 8 columns

  %aside.fifth  Fifth: 6columns
  %article.sixth  Sixth: 6 columns

  %aside.seventh  Seventh: 8 columns
  %article.eighth  Eigth: 4 columns

  %aside.ninth  Ninth: 10 columns
  %article.tenth  Tenth: 2 columns
```

Sass:
``` sass
body
  color: white
  background-color: white

.container 
  +outer-container
  background-color: tomato

aside, article
  height: 200px
  margin-bottom: 5px

article
  background-color: Olive

aside
  background-color: LightSkyBlue

.first
  +span-columns(2)
.third
  +span-columns(4)
.fifth
  +span-columns(6)
.seventh
  +span-columns(8)
.ninth
  +span-columns(10)

.second
  +span-columns(10)
.fourth
  +span-columns(8)
.sixth
  +span-columns(6)
.eighth
  +span-columns(4)
.tenth
  +span-columns(2)

.second, .fourth, .sixth, .eighth, .tenth
  +omega
```

#### Screenshot

{% img /images/Neat_01/span-columns.png %}

http://codepen.io/vis-kid/pen/Mayorb

Of course such an example is just for show and makes little practical sense . As you can see, every row consists of one blue **aside** on the left and one green **article** element on the right. The layout doesn’t break because their total number of columns add up to 12 (as defined in **$grid-columns**) evenly.

The coolest part is that there is no need to add any styling information to your markup—since this is related to your presentation layer, you only add the info how your grid is composed of to your Sass files. Cleanly separated concerns. Every sane designer that touches your work after you will love you for not polluting the content with styling information.


### Nesting columns

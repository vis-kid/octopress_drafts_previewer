---
layout: post
title: Bourbon Neat-Mixins 01
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

The outer container holds your layout grid. Within it, your grid can span as many columns across as specified in your **grid-settings** file via the **$grid-columns** variable (defaults to 12 columns). That means all the elements in a row have to add up to the total number of columns specified there. 


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

aside, article
  margin-bottom: 5px
  height: 200px

aside
  +span-columns(3)
  background-color: LightSkyBlue 

article
  +span-columns(9)
  +omega
  background-color: Olive
```

The background colors are supposed to make it easier to see how the pieces fit together. You don’t need to concern yourself too much about the **span-columns** and **omega** mixins in this example for now. After a couple of paragraphs they will be crystal clear to you.

#### Screenshot

{% img /images/Neat_01/outer-container.png %}

Here is also a codepen for playing around with it:
[codepen example](http://codepen.io/vis-kid/pen/xwZJOP)

In your settings file you can also specify a **$max-width** Sass variable that defines the maximum width that the content of your page should span. For example, Neat comes with a easily changeable **$max-width** setting of 1088px (converted to em) out of the box. That setting basically specifies the width of your outer container elements.

There is also the option to provide this mixin with an argument for a **$local-max-width** if you want a certain container element to have a different **max-width** than the global one set in **grid-settings**. This overwrites locally the default **max-width** from your settings file. You can provide *pixel*, *em* or *percentage* arguments. The columns of your grid inside that container adjust their width automatically but the number of columns stays the same.

Sass:
```sass
.container
  +outer-container(800px)
  background-color: tomato
```
or

```sass
.container
  +outer-container(80%)
  background-color: tomato
```

[codepen example](http://codepen.io/vis-kid/pen/vNGOqj)

+ ### span-columns

Just in case some of you are new to designing with grids, you should maybe look into this excellent book by [Khoi Vinh](http://www.subtraction.com/2010/11/05/i-wrote-a-book/). I highly recommend it. One concept that you need to understand right away though is that you build up your grid designs through a series of columns that span across the page. 

The basic usage of this is super straightforward in this framework. You just pick an element and tell it how many columns it should span within the total number of **$grid-columns**. Let me demonstrate the basics. 

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
  %article.eighth  Eighth: 4 columns

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

[codepen example](http://codepen.io/vis-kid/pen/Mayorb)

Of course such an example is just for show and makes little practical sense . As you can see, every row consists of one blue **aside** on the left and one green **article** element on the right. The layout doesn’t break because within the outer container element their total number of columns add up to 12 (as defined in **$grid-columns**) evenly.

The coolest part is that there is no need to add any styling information to your markup—since this is related to your presentation layer, you only add the info how your grid is composed of to your Sass files. Cleanly separated concerns. SWEET! Every sane designer that touches your work after you will love you for not polluting the content with styling information. Your future self anyway!


An added bonus that comes for free is that you can name classes in your markup anyway you want / need. Nobody makes these decisions for you which is a blessing without any disguise. Yes naming is hard, yadda yadda yadda, but its even trickier if somebody unrelated makes these decisions for you.

### Nesting columns

Sass:
``` sass
.some-parent-element
  +span-columns(10)

.some-nested-element
  +span-columns(5 of 10)
```

Of course, from time to time it might come in handy to quickly nest grid elements within another. Say you have a wide element that spans for 10 columns and should incorporate two smaller elements spanning 5 columns each. Easy! All you have to provide the nested elements with is the size of the parent column as an argument to the **span-columns** mixin. The nested elements can of course only add up to the number of columns of the parent tops. That’s it! Let’s look at a more concrete example.

Haml:
```haml
.container
  %aside.first First: 2 columns
  %article.second
    %article.third Third: 5 nested columns
    %article.fourth Fourth: 5 nested  columns
```

Sass:
```sass
@import "bourbon"
@import "neat"

body
  color: white
  background-color: white

.container
  +outer-container
  background-color: tomato
  padding:
    top: 15px
    bottom: 15px

aside, article
  height: 200px

article
  background-color: Olive

aside
  background-color: LightSkyBlue

.first, .second
  height: 250px
  
.second
  +span-columns(10)
  
.third, .fourth
  +span-columns(5 of 10)
  background-color: darken(Olive, 6)
  margin-top: 25px
  
.first
  +span-columns(2)
  padding-top: 25px
```

#### Screenshot

{% img /images/Neat_01/span-columns-nested.png %}

[codepen example](http://codepen.io/vis-kid/pen/VvaWBV)

+ ### omega

Another important concept for newbies playing with grids is the gutter. It’s just the margin on the right between grid elements and get’s automatically created for every grid element in a container—except for the last! Gutters also scale responsively if you resize the browser window. The example below clearly demonstrates this space beween grid elements. The gutter is signified by the tomato-colored background which comes through from the outer container. 

#### Screenshot
{% img /images/Neat_01/gutters.png %}

Haml:
```haml
.container
  .first  1 column
  .second 2 columns
  .third  3 columns
  .fourth 3 columns
  .fifth  2 columns
  .sixth  1 column
```

Sass:
```sass
body
  color: white
  background-color: white

.container
  +outer-container
  background-color: tomato

.first, .second, .third, .fourth, .fifth, .sixth
  background-color: Olive
  height: 200px
  
.first
  +span-columns(1)
  
.second
  +span-columns(2)
   
.third
  +span-columns(3)
  
.fourth
  +span-columns(3)
  
.fifth
  +span-columns(2)
  
.sixth
  +span-columns(1)
```

[codepen example](http://codepen.io/vis-kid/pen/avNPjX)

Easy-peasy right? But guess what happens if we just double the columns by duplicating the row right beneath it?

#### Screenshot

{% img /images/Neat_01/messy-columns-without-omega.png %}

[codepen example](http://codepen.io/vis-kid/pen/BoKvER)

Pretty messy huh? So what happened here? Because the 6th element in the first row is not the last element anymore, it also get’s a right gutter (margin) by default. Let me be very clear on this—to achieve a cleanly aligned layout, the last element in a container has it’s gutter removed by default. Because of the added gutter on the sixth element, the width of all elements in the first row now exceed the **total-width** your number of **total-columns** can span per row and your grid simply breaks. 

Nothing too tragic though and the fix is easy. Just find the element that needs that automatically added gutter to the right removed and apply the **omega** mixin there. Boom, that easy!

Haml:
```sass
.container
  .first  1st: 1 column
  .second 2nd: 2 columns
  .third  3rd: 3 columns
  .fourth 4th: 3 columns
  .fifth  5th: 2 columns
  .sixth  6th: 1 column
  
  .first  1st: 1 column
  .second 2nd: 2 columns
  .third  3rd: 3 columns
  .fourth 4th: 3 columns
  .fifth  5th: 2 columns
  .sixth  6th: 1 column
```

Sass:
``` sass
@import "bourbon"
@import "neat"

body
  color: white
  background-color: white

.container
  +outer-container
  background-color: tomato

.first, .second, .third, .fourth, .fifth, .sixth
  background-color: Olive
  height: 200px
  margin-bottom: 5px
  
.first
  +span-columns(1)
  
.second
  +span-columns(2)
   
.third
  +span-columns(3)
  
.fourth
  +span-columns(3)
  
.fifth
  +span-columns(2)
  
.sixth
  +span-columns(1)
  +omega
```

As you can see, I only added an **omega** to the **.sixth** class but it made all the difference. Every element falls into place nicely and none of the rows exceed their **total-width**.

{% img /images/Neat_01/messy-columns-with-omega.png %}

[codepen example](http://codepen.io/vis-kid/pen/BoKMow)

Let’s take this one little step further. Say you have a couple of rows that should display images of the same size evenly without breaking the grid. All we need is a couple of elements that span the same width, here **span-columns(2)**, and place them in a couple of rows. The magic happens with the arguement you supply the **omega** with:

```sass
img
  +omega(6n)
```
So every sixth **img** element will have it’s right gutter removed and therefore evenly fits six 2-column elements into the 12 columns of the outer container. Neat! 

Haml:
```haml
.container
  %img
  %img
  %img
  %img
  %img
  %img
  
  %img
  %img
  %img
  %img
  %img
  %img
  
  %img
  %img
  %img
  %img
  %img
  %img
  
```

Sass:
```sass
body
  color: white
  background-color: white

.container
  +outer-container
  background-color: tomato

img
  +span-columns(2)
  +omega(6n)
  height: 200px
  margin-bottom: 5px
  background-color: Olive
```

#### Screenshot

{% img /images/Neat_01/omega(6n).png %}

[codepen example](http://codepen.io/vis-kid/pen/NGNoXB)

You want only 4 elements per row? No problem! Just reduce the argument for **omega** to **4n**. This will come in handy in the next article when we get to responsive grids and how you can change your layout through media queries.

Sass:
```sass
img
  +omega(4n)
```

{% img /images/Neat_01/omega(4n).png %}

[codepen example](http://codepen.io/vis-kid/pen/meEbdr)

I enrourage you to play around with this example via the provided codepen and get a feel for it. There is no magic here, but if you need a bit more time to wrap your head around, mess a bit with the arguments of the omega and I have no doubt it will become crystal clear to you in no time.


#### Attention!
Last words of wisdom: In some cases it seems to matter in which order you supply the **span-columns** and **omega** mixins to the elements. My advice is to always use **span-columns** first to avoid unexpected behaviour.

+ ### shift

This one should be quick. If you want to adjust an element by moving it horizontally to the left or right, you simply apply the **shift** mixin and provide it with the number of columns it should move. You can use integers or floating point numbers.

Sass:
```sass
.some-element-that-needs-adjusting
  +shift(n)
```

Provide a positive number (unitless) of columns and the element moves to the right and vice versa. Behind the scenes, Neat simply increases or decreases the percentage values of **margin-left** on the element. Little sidenote, if you use **shift** without any argument, it will default to **shift(1)**.

Screenshot without **shift**:

{% img /images/Neat_01/without-shift.png %}

Screenshot with **shift(1)**:

{% img /images/Neat_01/with-shift(1).png %}

Screenshot with **shift(-2)**:

{% img /images/Neat_01/with-shift(-1.5).png %}

[codepen example](http://codepen.io/vis-kid/pen/XmKrmQ)

+ ### shift-in-context

Same idea as shift (it uses **shift-in-context** under the hood btw) but made for grid elements that are nested I guess. Played around with it a little bit in a dummy example that had nested grids but achieved the same results using only **shift**.  

+ ### pad

I think I don’t need to go into any specifics how this little padding fella works. It’s a little helper to clean  up your stylesheets and to provide you with the default gutter width if you provide the mixin with **default** as an argument. Nothing too fancy, but I thought I mentioned it to complete your options for adjusting the spacing of your grids.

Sass:
```
.some-element-that-needs-padding
  +pad(10px 20px 30px default)
```

Here you go, all you need to know to get started for playing with Neat grids. I tried to provide you with a solid basis that enables you to build any grid you need—however complex you like. Neat is one of my favorite tools out there and I hope I could show you why this lightweight project deserves a lot of respect. 

My next article will show you another round of Neat mixins and also explain how you can use media-queries and breakpoints to adjust your grids for changing viewport sizes.

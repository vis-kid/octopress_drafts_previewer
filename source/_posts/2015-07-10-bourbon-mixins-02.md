---
layout: post
title: "Bourbon - Mixins #02"
date: 2015-07-10 15:23:35 +0100
comments: true
sharing: true
categories: [ Sass, Mixins, Bourbon, Neat, Semantics, Thoughtbot, CSS, Design, Pixel Density, Media Query, HIDPI ] 
---

{% img /images/bourbon-mixins/bourbon-mixins02.jpg 550 %}

[{% img /images/bourbon-mixins/bourbon-logo@2x.png  250 450 %}](http://bourbon.io/)


[ -- **Context: Rails 4 apps** -- ] 

[ -- **Requirements: Sass 3.2+** -- ]

## Another Short List Of Goodies

+ **inline-block mixin**
+ **position mixin**
+ **triangle mixin**
+ **clearfix mixin**
+ **button mixin**
+ **size mixin**
+ **retina-image mixin**
+ **HIDPI-media-query mixin**

Let's take a look at these mixins in more detail.

<!-- more -->

*Examples below represent not necessarily best design practices but are chosen for exploring the basic functionality of these mixins.*

##  

+ ### inline-block mixin

``` html paragraphs' default display behaviour is block
<p class='paragraphs-behave-like-blocks'>
  Yada yada yada
<p class='paragraphs-behave-like-blocks'>
  Yada yada yada
```
#### Screenshot

{% img /images/bourbon-mixins/display-block.png %}

Block-level elements, like paragraphs, automatically create a new row in the layout.


This mixin comes in handy if you want to change the **default display behaviour** of elements to **inline-block**, instead of floating elements for example. It not only sets **display: inline-block** but also takes care of quirks and legacy support for IE7.

``` sass blocks with float-like behaviour through inline-block
.paragraphs-behave-like-blocks
  +inline-block
  background-color: tomato

//  SCSS syntax

    .paragraphs-behave-like-blocks {
      @include inline-block;
//  }
```

``` css CSS output
.paragraphs-behave-like-blocks {
  display: inline-block;
  vertical-align: baseline;
  zoom: 1;
  *display: inline;
  *vertical-align: auto;
  background-color: tomato;
}
```

#### Screenshot

{% img /images/bourbon-mixins/display-block-inline.png 400 %}

Set to display: inline-block, the paragraphs get displayed inline but retain their block-level characteristics.


### Attention

Notice the whitespace between the block elements. If you'd be using **float**, you wouldn't see any whitespace. It's a kind of default whitespace that doesn't go away by setting margins to 0px. Instead set **margins to -4px** if you want to get rid of it.

Learn more about [display](http://designshack.net/articles/css/whats-the-deal-with-display-inline-block/) here.

##  

+ ### position mixin

This mixin is a shorthand for writing something like this —

```sass
.some-element
  position: relative
  top: 0px
  left: 100px
```
— in just one line

``` sass
.some-element      
  +position(relative, 0px 0 0 100px)
                    //top right bottom left

// SCSS syntax

   .some-element
//   @include position(relative, 0px 0 0 100px);
```

That's it. No magic, but still super useful.

##  

+ ### triangle mixin

Want to use CSS **triangles** without fiddling around? There is certainly no more use for images to do the job.  

It's easy as pie with this:

```sass
.triangle
  +triangle(25px, tomato, down)
         // size, color, direction

// SCSS syntax

   .triangle {
     @include triangle(25px, tomato, down);
// }
```
The third parameter defines the direction.
Options for this mixin include:

**down** — **up** — **left** — **right**

**up-right** — **up-left** — **down-right** — **down-left**

#### Screenshot

{% img /images/bourbon-mixins/triangles.png 400 %}

##  

+ ### clearfix mixin

Wrappers that have floated elements inside have the [**zero-height container problem**](http://complexspiral.com/publications/containing-floats/) — in essence the container element inflates to zero pixels if all its elements inside are floated and therefore taken out of the containers *flow*, which leaves nothing left to fill the container -> inflates to zero. 

The clearfix mixin takes care of this when **applied to the container / wrapper element**. The best thing about this is that it doesn't require addional "empty" markup to accomodate the clearfix. It stays semantic by just adding the clearfix in your stylesheets.

Take a look at this very simple example:

``` html
<div class='grey-wrapper'>
  <div class='logos'>
```

``` sass
.grey-wrapper
  +clearfix        //grey
  background-color: #D4D4D4
  .logos
    float: right
    +background-image(url("bourbon-logo@2x.png"), url("thoughtbot.png"))
    background-position: center top, top left
    background-repeat: repeat-y, repeat-x
    height: 220px
    width: 50%

// SCSS syntax

// .grey-wrapper {
     @include clearfix;
     background-color: #D4D4D4;
     .logos {
       float: right;
       @include background-image(url("bourbon-logo@2x.png"), url("thoughtbot.png"));
       background-position: center top, top left;
       background-repeat: repeat-y, repeat-x;
       height: 220px;
       width: 50%;
     }
// } 
```

### Using clearfix

#### Screenshot

{% img /images/bourbon-mixins/with-clearfix.png %}

The grey container expands to hold the floated logos in it.

### Without clearfix

#### Screenshot

{% img /images/bourbon-mixins/no-clearfix.png %}

The grey container inflates to zero pixels because there is nothing in its "flow" to fill it. The wrapper is still there but you can't see it.

### Under The Hood

If you take a look at the source code it becomes clear how this **micro clearfix** works:

```sass source code https://github.com/thoughtbot/bourbon/blob/master/app/assets/stylesheets/addons/_clearfix.scss
@mixin clearfix {
  &:after {
    content:"";
    display:table;
    clear:both;
  }
}
```
Instead of creating an "empty" tag in your markup right before the closing tag of the container element and apply a clearfix there, this mixin uses the **&:after** pseudo element and places an **empty string** at the very end of the container and clears the float. That way it mimics content after the logo and tricks the browser to expand the grey container to surround other elements inside.

### Critique

I wonder if it would make more sense to use a **Sass placeholder selector** for the clearfix and use an **@extend** instead on an **@include**. I think one could argue that such an approach would be more leightweight and makes sense if you'd have lots of elements with clearfixes — of course this would mean an inconsistency in the Bourbon API though ...

##  

+ ### button mixin

Need high quality buttons out of the box?

#### Standard Button

{% img /images/bourbon-mixins/bourbon-buttons/bourbon-button.png 250 %}

```html
<div class='super-duper-button'>Super duper button</div>
```

```sass standard button
$light-blue: #2485F1 

.super-duper-button
  +button($light-blue)

// SCSS syntax

// .super-duper-button {
     @include button($light-blue);
// }
```
That's it, simple and semantically clean buttons. They even come with great looking subtle hover effects.

There are two more options:

#### Pill Button

{% img /images/bourbon-mixins/bourbon-buttons/pill-button.png 250 %}

```html
<div class='super-duper-button'>Super duper pill button</div>
```

```sass pill button
$dark-pink: #6B32D1

.super-duper-button
  +button(pill, $dark-pink)

// SCSS syntax

// .super-duper-button {
     @include button(pill, $dark-pink);
// }
```

#### Shiny Button

{% img /images/bourbon-mixins/bourbon-buttons/shiny-button.png 250 %}

```html
<div class='super-duper-button'>Super duper shiny button</div>
```

```sass shiny button
$acceptance-green: #43C914 

.super-duper-button
  +button(shiny, $acceptance-green)

// SCSS syntax

// .super-duper-button {
     @include button(shiny, $acceptance-green);
// }
```
### Attention!
Please note that the text on the buttons automatically changes its color depending on the **lightness** of the base-color of the button. That way the mixin provides better **contrast and readability**. Awesome!

{% img /images/bourbon-mixins/bourbon-buttons/grey-shiny-button.png 250 %}

```sass text adapts to the color of the button 
$light-grey: #DEDEDE

.super-duper-button
  +button(shiny, $light-grey)

// SCSS syntax

// .super-duper-button {
     @include button(shiny, $light-grey);
// }
```

##  

+ ### size mixin

Want to define **width** and **height** in one declaration?
All you need to do is:

```sass size mixin
.small-article
  +size(300, 400)

// SCSS syntax

// .small-article {
     @include size(300, 400);
// }
```

#### CSS Output
```css output
.small-article {
  width: 300px;
  height: 400px;
}
```
You can provide **px** values or just numerical values.
Of course you can use **auto** for these values as well.
If you provide only one size, Bourbon assumes you want a square.

```sass square
.square
  +size(25px)

// SCSS syntax

// .square {
     @include size(25px);
// }
```

##  

+ ### HIDPI-media-query mixin

If you want to quickly generate completely vendor prefixed media queries for detecting **HIDPI** ( "Retina" ) devices, this mixin comes in handy.

All you need to do is provide a target **device pixel ratio** and declarations that change if the media query detects devices with this or higher ratio. The default ratio is 1.3.

```sass HIDPI-media-query
.image
  width: 150px
  +hidpi(1.5)
    width: 200px

// SCSS syntax

// .image {
     width: 150px;
     +hidpi(1.5) {
       width: 200px;
     }
// }
```

```sass CSS output
.image {
  width: 150px;

  @media only screen and (-webkit-min-device-pixel-ratio: 1.5),
    only screen and (min--moz-device-pixel-ratio: 1.5),
    only screen and (-o-min-device-pixel-ratio: 1.5/1),
    only screen and (min-resolution: 144dpi),
    only screen and (min-resolution: 1.5dppx) {

    width: 200px;
  } 
}
```

##  

+ ### retina-image mixin

Depending on the **pixel density** of the device displaying your designs, you can provide images with the **appropriate bitmap resolution**. This fine mixin provides a **retina background-image** or a **non-retina background-image** — depending on the result of the mixin's internal **HIDPI-media-query** checking the device for its pixel density. 

If I'm not mistaken, as a bonus, it will serve only one of the images to avoid downloading both — which is especially advantageous for **mobile devices** with lower pixel densities. If the above is all *gobbledygook* to you, I'd recommend starting with this fantastic [article.](http://coding.smashingmagazine.com/2012/08/20/towards-retina-web/)

```sass retina-image syntax
.logo
  +retina-image(logo-icon, 50px, 30px)

// SCSS syntax

// .logo {
     @include retina-image(logo-icon, 50px, 30px);
// }
```

The CSS output helps explain its functionality:

```css CSS output
.logo {
  background-image: url(logo-icon.png); 
}

@media only screen and (-webkit-min-device-pixel-ratio: 1.3), 
       only screen and (min--moz-device-pixel-ratio: 1.3), 
       only screen and (-o-min-device-pixel-ratio: 1.3 / 1), 
       only screen and (min-resolution: 125dpi), 
       only screen and (min-resolution: 1.3dppx) {
  .logo {
    background-image: url(logo-icon_2x.png);
    background-size: 50px 30px; 
  }
}
```

The default of using a **device pixel ratio of 1.3** targets Apple "Retina" devices (ratio of 2) as well as devices with pixel ratios as "low" as 1.3. 

This mixin expects a **.png** as standard extension for all images. Per default, it also expects a **_2x.png** extension to the filename of your retina-image. Of course you can overwrite both defaults by providing another retina-filename and standard extension like this:

```sass 
.logo                                     
  +retina-image(logo-icon, 50px, 30px, 
                $extension: jpg, 
                $retina-filename: HIDPI-logo-icon, 
                $retina-suffix: _retina )

// SCSS syntax

// .logo {
     @include retina-image(logo-icon, 50px, 30px, 
                $extension: jpg, 
                $retina-filename: HIDPI-logo-icon, 
                $retina-suffix: _retina );
// }
```

```css CSS output
.logo {
  background-image: url(logo-icon.jpg); 
}

@media only screen and (-webkit-min-device-pixel-ratio: 1.3), 
       only screen and (min--moz-device-pixel-ratio: 1.3), 
       only screen and (-o-min-device-pixel-ratio: 1.3 / 1), 
       only screen and (min-resolution: 125dpi), 
       only screen and (min-resolution: 1.3dppx) {
  .logo {
    background-image: url(HIDPI-logo-icon_retina.jpg);
    background-size: 50px 30px; 
  }
}
```

There is another option if you are using the asset pipeline:

```sass 
.logo                                     
  +retina-image(logo-icon, 50px, 30px,
                $asset-pipeline: true)

// SCSS syntax

// .logo {
     @include retina-image(logo-icon, 50px, 30px,
                           $asset-pipeline: true);
// }
```

```css CSS output
.logo {
  background-image: url(logo-icon.png); 
}

@media only screen and (-webkit-min-device-pixel-ratio: 1.3), 
       only screen and (min--moz-device-pixel-ratio: 1.3), 
       only screen and (-o-min-device-pixel-ratio: 1.3 / 1), 
       only screen and (min-resolution: 125dpi), 
       only screen and (min-resolution: 1.3dppx) {
  .logo {          /* Attention! */
    background-image: image-url(logo-icon_2x.png);
    background-size: 50px 30px; 
  }
}
```
### *Attention!
Notice that in the CSS output **url** is replaced with **image-url**. This is a rails helper method that translates to  **url(/assets/logo-icon_2x.png)**.
It's a small difference but adds to your convenience developing with rails if you are using the **asset pipeline**.

### Critique
The documentation says 

{% blockquote #retina-image http://bourbon.io/docs/#retina-image Bourbon documentation %}
"The mixin is a helper to generate a retina background-image and non-retina background-image."
{% endblockquote %}

I think its fair to say that this is confusing because the mixin does not generate the "Retina" image itself. You still have to render the image with the appropriate amount of bitmap pixels that you need for larger resolution screens in your favorite graphics editing program yourself. 

In that regard, what this mixin generates is a consistent naming schema for the **values** of **background-image properties** which expect to be able to **serve images with higher resolutions** from your **assets** if the media-query encounters devices with a **device pixel ratio** of **1.3** or higher.

##  

That's it for mixins. Next I will look into functions: <li><a href="{{ root_url }}/blog/2014/01/29/bourbon-functions/">Bourbon: Functions</a></li>

{% img /images/bourbon-mixins/bourbon-anime.gif 700 %}

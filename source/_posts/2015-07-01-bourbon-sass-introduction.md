---
layout: post
title: "Bourbon - Connoisseurs’ Choice Sass Mixins"
date: 2015-07-01 18:34:54 +0100
comments: true
sharing: true
categories: [ Sass, Mixins, Bourbon, Neat, Semantics, Thoughtbot, CSS, Design ]
---

{% img /images/bourbon-mixins/david_niven.jpg 600 360  %}

[{% img /images/bourbon-mixins/bourbon-logo@2x.png  250 450 %}](http://bourbon.io/)

## What’s ahead?

+ **Introduction**
+ [**Setup**](#setup)
+ [**Mixins Overview**](#mixins)
+ [**Functions Overview**](#functions)
+ [**Add-ons**](#addons)
+ [**Conclusion**](#conclusion)

<!--more-->

## An Introduction

[Bourbon](http://bourbon.io) is a top-notch Sass **library for designers**. It has a minimalistic approach and is serious about creating quality code that cares about **semantics**. I like it especially because it encourages best practices for developing great code that scales.

This gem helps designers to write their code faster and manages a lot of the nitty-gritty details like annoying vendor prefixes. It’s mixins often act as wrappers for outputting quality CSS but stay as vanilla as possible by being close to the original CSS syntax.

Big heavyweight frameworks like [Bootstrap](http://getbootstrap.com) certainly have their charms. Developers like them because most design decisions are already made and reasonably decent looking websites come out pretty much prefabricated. There's nothing wrong with that - given that you want to bootstrap your project without a designer who values semantic markup. I should mention though that once designers are involved they might not be happy with that choice. Having to manually rip out unsemantic styles that are intertwined  with your markup might give them ideas for putting funny stuff in your coffee.

### Worth Pointing Out

+ Bourbon is pure Sass & platform agnostic, it works with any Sass project
+ Very close to the vanilla CSS syntax
+ Not tied to Ruby, unlike Compass
+ Includes awesome functions
+ Outsources vendor prefixes
+ It’s super lightweight
+ It’s semantic

## <a name='setup'></a>Setup

+ Fire up your terminal, change into your project’s directory and install Bourbon via RubyGems

``` bash Terminal
$ gem install bourbon
```

+ Change into your project’s stylesheets directory and generate your **bourbon folder**

``` bash Terminal
$ bourbon install
```

This command generates a bourbon folder that contains the functions, mixins, helpers, settings, etc. that you need. Don’t touch this folder. You will have a much better experience updating Bourbon in the future.

#### Screenshot

{% img /images/bourbon-introduction/bourbon-install-generated-folder.png %}

+ You need to finish the setup by importing the generated `sass` files in your stylesheets. Import Bourbon on top of your **application.sass** file and make sure you only import other Sass files below.

```sass application.sass
@import 'bourbon/bourbon'
@import 'other-sass-partials-below'
```

## <a name='mixins'></a>Mixins Overview

Bourbon has a wide range of super useful mixins to speed up your work. In terms of design, it’s safe to say that they want to support your own design decisions without forcing a particular style on you. You are basically encouraged to mix your own awesome sauce. You can use these mixins as a stable and relatively neutral basis.

Here are a couple of mixins you might wanna check out first:

+ `background-image`
+ `border-radius`
+ `box-sizing`
+ `linear-gradient`
+ `transitions`
+ `animations`
+ `font-face`
+ `button`
+ `triangle`
+ `retina-image`
+ `position`
+ `size`
+ `clearfix`
+ `inline-block`

Learn more about individual Bourbon mixins in my other articles here:

* <a href="{{ root_url }}/blog/2015/07/05/bourbon-mixins/">Bourbon: Mixins #01</a>
* <a href="{{ root_url }}/blog/2015/07/10/bourbon-mixins-02/">Bourbon: Mixins #02</a>

## <a name='functions'></a>Functions Overview

Sass already has a ton of built-in functions, from manipulating strings to messing with opacity and colors. Bourbon adds a couple of selected enhancements and provides very handy Sass functions for a variety of use cases. Take a look a this selection:

+ `golden-ratio()`

With this function, it is very easy to calculate the golden ratio of a certain number (slowly depricated though). It is useful if you want to create “meaningful” relationships within the sizes of your headers and body copy for example. The same goes for structural relationships in your layout.

+ `linear-gradient()`

If you need a linear gradient in combination with your background-image mixin, this function will save you quite a bit of code.

+ `tint()`
+ `shade()`

You might already be familiar with Sass's built in functions for colors like **lighten()** and **darken()** which do exactly what you'd expect. Bourbon provides two additional awesome color functions for your convenience. The tint function changes a color by mixing it with white. The shade function changes a color by mixing it with black. Both functions take a color and percentage parameter to fine-tune the color mix.

+ `modular-scale()`

If you are into "*more meaningful typography*" and want to calculate a modular scale for varying font sizes that have some sort of numerical relationship, modular-scale() might become your new best friend.

+ `em()`

Calculates **pixels to ems** for you.

I have prepared a more detailed look at functions here:
<a href="{{ root_url }}/blog/2014/01/29/bourbon-functions/">Bourbon: Functions</a>

## <a name='addons'></a>Add-ons


+ ### **font-family variables**

Bourbon defines a small but useful set of default variables for font-families.

  + `$helvetica`
  + `$georgia`
  + `$lucida-grande`
  + `$monospace`
  + `$verdana`

Instead of typing —

``` sass traditional way of defining fonts
font-family: "Helvetica Neue", Helvetica, Arial, sans-serif
```

— you just use one of the font-family variables.

``` sass font-family variable
font-family: $helvetica
```

+ ### **timing variables**

A mixin like `transition` has the following syntax:

``` sass
.some-element
  +transition(all 5s $ease-in-circ)

//  SCSS syntax
//  .some-element {
      @include transition(all 5s $ease-in-circ);
//  }
```

The last parameter defines it’s **timing**. You can fine-tune the transitional behaviour by providing one of 24 variables to control timing. The GIF belows illustrates the options best:

{% img /images/bourbon-mixins/timing-functions.gif %}

## <a name='conclusion'></a>Conclusion

Do your future self and your colleagues a favor and give [Bourbon](http://bourbon.io) a shot. This gem puts a high value on **semantic markup** while being **lightweight & simple**. It will serve you well with your design and CSS architecture plus it aids cultivating best practices for creating superb code.

{% img /images/bourbon-mixins/LaForge.gif 600 %}

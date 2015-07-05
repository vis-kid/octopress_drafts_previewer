---
layout: post
title: "Bourbon - Mixins #01"
date: 2015-07-05 19:55:32 +0100
comments: true
sharing: true
categories: [ Sass, Mixins, Bourbon, Neat, Semantics, Thoughtbot, CSS, Design ] 
---

{% img /images/bourbon-mixins/bourbon-mixins.jpg 550 %}

[{% img /images/bourbon-mixins/bourbon-logo@2x.png  250 450 %}](http://bourbon.io/)

## A Short List Of Goodies

+ **background-image mixin**
+ **linear-gradient mixin**
+ **border-radius mixin**
+ **box-sizing mixin**
+ **transition mixin**
+ **font-face mixin**

Let's take a look at these mixins in more detail.

<!-- more -->

*The examples below represent not necessarily best design practices but are chosen for exploring the basic functionality of these mixins*.

##  

+ ### background-image mixin

Creates a background-image property comprised out of multiple ( 1 up to 10 ) comma delimited **background images** and / or **linear- / radial-gradients**.

##### With images

``` html
<div class='ridiculous-background'>
```

``` sass background-image mixin with images
.ridiculous-background
  +background-image(url("bourbon-logo@2x.png"), url("thoughtbot-logo.png"))

  // chain the positions for the images
  background-position: center top, top left
  // chain the repeat values
  background-repeat: repeat-y, repeat-x

  height: 560px

//  SCSS syntax
//  .ridiculous-background {
      @include background-image(url("bourbon-logo@2x.png"), url("thoughtbot-logo.png"));
      background-position: center top, top left;
      background-repeat: repeat-y, repeat-x;
      height: 560px;
//  }
```

#### Screenshot
{% img /images/bourbon-mixins/background-image-mixin.png %}

### Attention!

Take a look at the precedence of layers -> The first image gets displayed on top.
The same goes for gradients.

#### Shorthand notation

You can use a shorthand notation for **background-image** like this:

``` sass shorthand notation for background-image mixin
.ridiculous-background
  +background(url("bourbon-logo@2x.png"), url("thoughtbot-logo.png")) 

//  SCSS syntax
//  .ridiculous-background
//    @include background(url("bourbon-logo@2x.png"), url("thoughtbot-logo.png")); 
```

##### With gradients

You can use of Bourbon's **linear-gradient function** inside the background-image mixin.

``` html 
<section class='super-duper-gradient'>
```

``` sass background-image mixin with gradients
.super-duper-gradient
	+background-image(
  // linear-gradient function
  linear-gradient(hsla(0, 100%, 100%, 0.25) 0%, hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
  linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)))
  height: 50px

//  SCSS syntax
//  .super-duper-gradient {
      @include background-image(
      linear-gradient(hsla(0, 100%, 100%, 0.25) 0%, hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
      linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)));
      height: 50px;
//  }
```

#### Screenshot
{% img /images/bourbon-mixins/backgroud-image_gradient.png %}

##  

+ ### linear-gradient mixin

``` html 
<section class='simple-gradient'>
```

``` sass linear-gradient mixin
.simple-gradient
  +linear-gradient(#268BD2, #7229d1)
  height: 200px

//  SCSS syntax
//  .simple-gradient {
      @include linear-gradient(#268BD2, #7229d1);
      height: 200px;
//  }
```

#### Screenshot

{% img /images/bourbon-mixins/linear-gradient-mixin.png %}

##  

+ ### border-radius mixin

``` html 
<section class='super-duper-borders'>
```

``` sass border-radius mixin
.super-duper-borders
  +background-image(
  linear-gradient(hsla(0, 100%, 100%, 0.25) 0%, hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
  linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)))

  // border-radii for every corner
  +border-top-radius(3px)
  +border-bottom-radius(3px)
  +border-left-radius(3px)
  +border-right-radius(3px)

  height: 50px


//  SCSS syntax
//  .super-duper-borders {
      @include background-image(
      linear-gradient(hsla(0, 100%, 100%, 0.25) 0%, hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
      linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)));
    
      @include border-top-radius(3px);
      @include border-bottom-radius(3px);
      @include border-left-radius(3px);
      @include border-right-radius(3px);
    
      height: 50px;
//  } 
```
#### Screenshot

{% img /images/bourbon-mixins/border-radius-mixin.png %}

Compare both gradients and focus your attention on the lower gradient which now has very subtle **3px rounded corners**. Too much rounding lets designs often look comical. Keep it simple!

##  

+ ### box-sizing mixin

Easily change the box model of an element. You have 3 options to choose:
  

**border-box | content-box | inherit**  

( [more about box sizing](http://css-tricks.com/box-sizing/) ) 

``` sass box-sizing mixin 
.user-profile
  +box-sizing(border-box)

//   SCSS syntax
//  .user-profile {
      @include box-sizing(border-box);
//  }
```

##  

+ ### transition mixin

You attach the transition mixin to the default state of the selector that is to be changed by an event like hover — **not to the pseudo-class!** The declarations used in the pseudo-class are available to the transition mixin.

``` html 
<section class='fancy-transition'>
```

``` sass transition mixin
.fancy-transition 
  +transition (all 1.0s ease-in-out)

  background-color: red
  +border-top-radius(5px)
  +border-bottom-radius(5px)
  +border-left-radius(5px)
  +border-right-radius(5px)
  height: 50px

  &:hover 
  // declarations available to the transitions mixin
  background-color: blue 
  +border-top-radius(25px)
  +border-bottom-radius(25px)
  +border-left-radius(25px)
  +border-right-radius(25px)
  

//  SCSS syntax
//  .fancy-transition {
      @include transition (all 1.0s ease-in-out);
    
      background-color: red;
      @include border-top-radius(5px);
      @include border-bottom-radius(5px);
      @include border-left-radius(5px);
      @include border-right-radius(5px);
      height: 50px;
    
      declarations available to the transitions mixin
      &:hover {
      background-color: blue;
      @include border-top-radius(25px);
      @include border-bottom-radius(25px);
      @include border-left-radius(25px);
      @include border-right-radius(25px);
      }
//  }
```
#### Screenshots

#### Normal state

{% img /images/bourbon-mixins/transition-mixin-red.png %}

It transitions over the specified time -> Here 1.0s

{% img /images/bourbon-mixins/transition.gif %}

#### Hover state after transition

{% img /images/bourbon-mixins/transition-mixin-blue.png %}

You can handpick the properties you want to be affected by the transition. Instead of  **all** use only the properties you need.

``` sass 
// all
@include transition (all 1.0s ease-in-out);

// fine-tuned
@include transition (background-color, height 1.0s ease-in-out);
```

To fine-tune transitional behaviour there are a number of very convenient **timing-functions** at your disposal:

{% img /images/bourbon-mixins/timing-functions.gif %}

##  

+ ### font-face mixin

As we know, typography is an essential piece of the puzzle of designing high quality projects for the web. As a kind of atomic structure it guides so many design decisions and can influence the perception of the user in a multitude of ways. 

**@font-face** allows designers to **customize type** an incredible amount by providing users with custom fonts which they might not have installed on their machines.

In the past I went to [fontsquirrel](http://fontsquirrel.com), which is awesome btw, used the **webfont generator** and pasted a lot of CSS like this into my stylesheets. Those days are gone.

``` scss styles generated by webfont generator pasted into a classic CSS stylesheet
@font-face {
    font-family: 'SourceSansPro-Regular';
    src: url('sourcesanspro-regular.eot');
    src: url('sourcesanspro-regular.eot?#iefix') format('embedded-opentype'),
         url('sourcesanspro-regular.woff') format('woff'),
         url('sourcesanspro-regular.ttf') format('truetype'),
         url('sourcesanspro-regular.svg#source_sans_proregular') format('svg');
    font-weight: normal;
    font-style: normal;
}

@font-face {
    font-family: 'SourceSansPro-Bold';
    src: url('sourcesanspro-bold.eot');
    src: url('sourcesanspro-bold.eot?#iefix') format('embedded-opentype'),
         url('sourcesanspro-bold.woff') format('woff'),
         url('sourcesanspro-bold.ttf') format('truetype'),
         url('sourcesanspro-bold.svg#source_sans_probold') format('svg');
    font-weight: normal;
    font-style: normal;
}

// applied custom fonts to some classes

.regular-font {
  font-family: SourceSansPro-Regular;
}

.bold-font {
  font-family: SourceSansPro-Bold;
}
```
Obviously this can get very tedious very quickly. Using Bourbon, it looks like this now:

``` sass font-face mixin
+font-face(SourceSansPro-Regular, 'SourceSansPro/sourcesanspro-regular', normal, $asset-pipeline: true) 
+font-face(SourceSansPro-Bold, 'SourceSansPro/sourcesanspro-bold', bold, $asset-pipeline: true)

.regular-font 
  font-family: SourceSansPro-Regular

.bold-font 
  font-family: SourceSansPro-Bold

//  SCSS syntax
//  @include font-face(SourceSansPro-Regular, 'SourceSansPro/sourcesanspro-regular', normal, $asset-pipeline: true); 
    @include font-face(SourceSansPro-Bold, 'SourceSansPro/sourcesanspro-bold', bold, $asset-pipeline: true); 
    
    .regular-font {
      font-family: SourceSansPro-Regular;
    }
    
    .bold-font {
      font-family: SourceSansPro-Bold;
//  }
```

Boom!! That easy!

###Under The Hood:

This mixin expects you to provide 

+ a **fonts** folder in assetts: **app/assets/fonts** where you store your webfonts 
— which I still generate with the **webfont generator** from **fontsquirrel** ->  eot, ttf, woff, svg formats of uploaded typefaces.

#### Screenshot

{% img /images/bourbon-mixins/app-assets-fonts.png %}

+ a **font-family** name for later use in your declarations
— can be any name you wish. Above I used **SourceSansPro-Regular** for example 

+ a **file-path** to find the custom font on the machine

+ **font-weight** like *normal* or *bold*

+ **font-style** like *normal*, *italic* or *oblique* ( maybe avoid oblique type! ) 

+ and a hash for using the **asset-pipeline** with rails or not

``` sass Breaking down the parts
             //font-family name      file-path                          weight  style 
+font-face(NameFor-font-family, 'SomeFontFolder/NameOfFont-Italic', normal, italic, $asset-pipeline: true)

// SCSS syntax
// @include font-face(NameFor-font-family, 'SomeFontFolder/NameOfFont-Italic', normal, italic, $asset-pipeline: true);
```

### Attention!
+ The order of the includes matters, and it is: 

**normal, bold, italic, bold+italic**
 
+ Do not use forward slashes for the folder your fonts are stored in!!
  It will fail like this:

```sass 
                                       !!!
+font-face(SourceSansPro-Regular, '/SourceSansPro/sourcesanspro-regular', normal, $asset-pipeline: true) 

//  SCSS syntax
//  @include font-face(/SourceSansPro-Regular, '/SourceSansPro/sourcesanspro-regular', normal, $asset-pipeline: true); 
```

## Cheers!

I have prepared another article about Bourbon's mixins: <li><a href="{{ root_url }}/blog/2014/01/27/bourbon-mixins-02/">Bourbon: Mixins #02</a></li>

{% img /images/bourbon-mixins/github-bourbon.gif %}

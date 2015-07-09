---
layout: post
title: "Bourbon - Functions"
date: 2015-07-15 12:51:46 +0100
comments: true
sharing: true
categories: [ Sass, Functions, Golden Ratio, Golden Mean, Divine Proportion, Bourbon, Neat, Semantics, Thoughtbot, CSS, Design ] 
---

{% img /images/bourbon-functions/bourbon-aerator.jpg 450 260  %}

[{% img /images/bourbon-mixins/bourbon-logo@2x.png  250 450 %}](http://bourbon.io/)

## Functions

Bourbon provides very handy Sass functions for a variety of use cases. Let’s take a look at this selection:

+ `linear-gradient()` 
+ `modular-scale()`
+ `golden-ratio()`
+ `shade()`
+ `tint()`
+ `em()`

<!-- more -->

##   

+ `golden-ratio()` function

With this function, it is very easy to calculate the golden ratio of a certain number (slowly depricated though). It is useful if you want to create “meaningful” relationships within your typography for example. The same goes for structural relationships in your layout.

### FYI: The Golden Ratio

{% img /images/bourbon-functions/bender-golden-ratio.jpg 280 %}

There are two sides to this story. The classical approach to describing the importance of this ratio could be along the following lines: 

The golden ratio or “***divine proportion***” is a very common ratio across disciplines. In music, art, mathematics, architecture, biology and in many other fields you can encounter the "use" of the ratio **1 : 1.6180339** to tie structures together. Interestingly, in the past, especially in early architecture, it was often used to please the "gods" by attempting to "communicate" in a "divine geometric language". 

This ratio represents a pattern that seems to please human visual perception, probably because it’s been so abundant in biology around us all along. Therefore we supposedly perceive some kind of inherent balance when confronted with it. For ages, applying this ratio was seen as an old "trick" used by creators because it can make your work feel more **harmonious** or even "natural". 

### Twitter’s use of the golden ratio for its layout

{% img /images/bourbon-functions/goldentwitter.jpg 580 %}


In design the use of the ***golden mean*** is widespread as well: 

+ Layouts in Webdesign
+ iPods / iPhones
+ Business cards 
+ Credit cards
+ Tyography
+ Postcards
+ Logos
+ Cars

...and [many more](http://en.wikipedia.org/wiki/List_of_works_designed_with_the_golden_ratio) 

On the other hand, you are also encouraged to doubt the tremendous reputation the golden ratio has earned over the centuries. Check out this controversial [article](http://www.fastcodesign.com/3044877/the-golden-ratio-designs-biggest-myth) if you want to whet your appetite. The tl;dr version is that it’s supposed to be bullshit and a very old scam. Maybe still useful and effective, but simply nonsense projected onto an irrational number. 

At Stanford hundreds of students were asked over the years about their preference of the golden ratio. The Haas School of Business in Berkeley did something similar. The conclusion was that in the real world people don’t prefer the golden ratio. I’ll leave you with that and you can decide for yourself if “divine” is really what we should call this proportion that is supposedly the universal formula behind aesthetic beauty. Maybe another case where we try to find meaning in the patterns out there.

### The golden ratio for your typographic scale

If you want to generate a [modular scale](http://www.hyperarts.com/blog/how-to-use-a-modular-scale/) to structure various sizes of type by using the golden ratio, you can apply the **goden-ratio function** to calculate the **golden mean** for any number. Building your **typographic scale** is straightforward:

```sass typographic scale using the golden ratio
$base-font-size: 10px

body
  font-size: $base-font-size

.footnote
  font-size: golden-ratio($base-font-size, -1)
                                        // decrement value
h3
  font-size: golden-ratio($base-font-size, 1)
                                        // increment value
h2
  font-size: golden-ratio($base-font-size, 2)

h1
  font-size: golden-ratio($base-font-size, 3)

//  SCSS syntax

//  $base-font-size: 10px;

    body {
      font-size: $base-font-size;
    }

    .footnote {
      font-size: golden-ratio($base-font-size, -1);
    }
    
    h3 {
      font-size: golden-ratio($base-font-size, 1);
    }
    
    h2 {
      font-size: golden-ratio($base-font-size, 2);
    }
    
    h1 {
      font-size: golden-ratio($base-font-size, 3);
//  }
```

```css CSS output
body {
  font-size: 10px;
}

.footnote {
  font-size: 6.18px;
}

h3 {
  font-size: 16.18px;
}

h2 {
  font-size: 26.179px;
}

h1 {
  font-size: 42.358px;
}
```

The function’s first parameter expects a **pixel** or **em** value — here represented by a Sass variable defined above. The second parameter requires an integer as **increment / decrement value** (...-3, -2, -1, 0, 1, 2, 3...) for scaling the provided font-size using **1.6180339**.
If you need to round the output, you can use Sass’s functions for: 

### **abs()** — **floor()** — **ceil()** 

```sass rounded example
.footnote
  font-size: floor( golden-ratio($base-font-size, -1) )

//  SCSS syntax

//  .footnote {
      font-size: floor( golden-ratio($base-font-size, -1) );
//  }
```

### Under The Hood

Internally the golden-ratio function is using the **modular-scale function** with the scaling variable **$golden** for golden-ratio.

``` sass modular-scale mixin
@function golden-ratio($value, $increment) {
  @return modular-scale($value, $increment, $golden)
}
```

Btw the fantastic [Bourbon Neat](http://neat.bourbon.io) grid framework also uses the golden ratio by default for **gutters** and **columns**.

##  

+ ### **modular-scale() function**

If you are into ["*more meaningful typography*"](http://www.alistapart.com/article/more-meaningful-typography) and want to calculate a modular scale for various font sizes that have some sort of numerical relationship, this function might be interesting to you.
It offers to calculate various modular scales for you — the golden ratio is just one out of **17 options**, excluding your very own made up scales of course. 


``` sass modular-scale example
$base-font-size: 10px
// Your choice of ratio saved in a variable to change it in one place
// Here I used the double-octave ratio
$type-of-scale: $double-octave

body
  font-size: $base-font-size

.footnote
  font-size: modular-scale($base-font-size, -1, $type-of-scale)

h3
  font-size: modular-scale($base-font-size, 1, $type-of-scale)

h2
  font-size: modular-scale($base-font-size, 2, $type-of-scale)

h1
  font-size: modular-scale($base-font-size, 3, $type-of-scale)

//  SCSS syntax

//  $base-font-size: 10px;
    $type-of-scale: $double-octave;

    body {
      font-size: $base-font-size;
    }

    .footnote {
      font-size: modular-scale($base-font-size, -1, $type-of-scale);
    }
    
    h3 {
      font-size: modular-scale($base-font-size, 1, $type-of-scale);
    }
    
    h2 {
      font-size: modular-scale($base-font-size, 2, $type-of-scale);
    }
    
    h1 {
      font-size: modular-scale($base-font-size, 3, $type-of-scale);
//  }
```

### Scaling Variables

{% img /images/bourbon-functions/scaling-variables.png 350 %}


Bourbon prepared these **variables** of **predefined ratios for various scales**. To create a consistent design, it would be a good decsision not to mix different ratios for your typographic scale in one project. Keep it classy by deciding on one ratio that mirrors your intentions best.

### Attention!

To use these scaling variables you need to install a version of Bourbon higher than **v3.1.8**.
As of Feb 2014, I’d recommend updating to the beta with 

+ tag 
  + **v3.2.0-beta.1**  
or
  + **v3.2.0-beta.2**

Put this in your gemfile to directly update from Github:

```ruby Gemfile
gem 'bourbon', git: 'https://github.com/thoughtbot/bourbon.git', tag: 'v3.2.0-beta.2'

```

and of course

```ruby
bundle install
```

Otherwise the variables above will throw an error. Of course you can just type in any desired ratio as the third parameter yourself.

```sass without scaling variables
$base-font-size: 10px

h2
  font-size: modular-scale($base-font-size, 2, 1.6180)
                                            // golden ratio

//  SCSS syntax

//  $base-font-size: 10px;

    h2 {
      font-sizse: modular-scale($base-font-size, 2, 1.6180);
//  }
```

## 

+ ### **linear-gradient()** function

If you need a linear gradient in combination with your background-image mixin, this function will save you quite a bit of code. The color of the gradient is defined by the **starting color**, the **ending color** and optional **stop-color points** in between. Those additional color-stops give you the possiblity to create more sophisticated transitions between the starting and ending colors, or provide a more colorful gradient. 

Take a look at this horrible gradient. Here I think it’s easy to see how the **linear-gradient** function works and how you can utilize it:

#### Screenshot

{% img /images/bourbon-functions/horrible-gradient.png %}

```sass horrible linear-gradient
.horrible-gradient
  +background-image(linear-gradient( 
    45deg,                // directon of gradient line 
    red 10%,              // starting color 
    yellow 15%,   // S    // bleeds into red     
    yellow 40%,   // T                           
    orange 45%,   // O    // bleeds into yellow   
    orange 50%,   // P                            
    orange 70%,   // S    // bleeds into green    
    green 90%)            // ending color
  )                       
    height: 50px
  
//  SCSS syntax

//  .horrible-gradient {
      @include background-image(linear-gradient( 
        45deg,                // directon of gradient line 
        red 10%,              // starting color 
        yellow 15%,   // S    // bleeds into red     
        yellow 40%,   // T                           
        orange 45%,   // O    // bleeds into yellow   
        orange 50%,   // P                            
        orange 70%,   // S    // bleeds into green    
        green 90%)            // ending color
      )                      
        height: 50px
//  }
```

For colors you can optionally provide **precentage**, **pixel** or **em** **values**. Those define the distance this color is supposed to stretch out. You should probably stick to using ***%*** most of the time though. If you don’t provide percentages as limitation values, the colors will strech out evenly, divided by the number of colors in the gradient.
You can optionally provide an angle for the first parameter — either in form of **value** + **deg** or 
**to** with **direction**:

+ 45deg
+ 90deg
+ to left top
+ to right
+ to left

and so on.

#### Screenshot

{% img /images/bourbon-functions/yellow-blue-gradient.png %}

The gradient flows from left to right

``` sass with direction parameter — left to right
.gradient
  +background-image(linear-gradient(to right, yellow 50%, blue 60%)) 
  height: 50px

//  SCSS syntax

//  .gradient {
      @include background-image(linear-gradient(to right, yellow 50%, blue 60%)); 
      height: 50px;
//  }
```


#### Screenshot

{% img /images/bourbon-functions/hsla-gradient.png %}

Something more sophisticated using **hsla()** functions and multiple **linear-gradient()** functions:

```sass decent gradient
.gradient 
  +background-image(linear-gradient(
    hsla(0, 100%, 100%, 0.25) 0%, 
    hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
    linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)))
  height: 50px

//  SCSS syntax

//  .gradient 
      +background-image(linear-gradient(
        hsla(0, 100%, 100%, 0.25) 0%, 
        hsla(0, 100%, 100%, 0.08) 50%, transparent 50%), 
        linear-gradient(#4e7ba3, darken(#4e7ba4, 10%)))
//    height: 50px
```

## Tint & Shade Color Functions 

You might already be familiar with Sass’s built in functions for colors like `lighten()` and `darken()` which do exactly what you’d expect. Bourbon provides two additional awesome color functions for your convenience. Both functions take a color and percentage parameter to fine-tune the color mix.

+ ### **tint() function**

The tint function changes a color by mixing it with **white**.
It expects a second parameter that takes the **percentage of white** you want to mix the color with.

```sass tint()
.tint
  background: tint(#2F7DD1, 25%)                                                               height: 100px

//  SCSS syntax

//  .tint {
      background: tint(#2F7DD1, 25%)                                                               height: 100px
//  }
```

#### Without tint()

{% img /images/bourbon-functions/no-tint.png %}

#### With tint()

{% img /images/bourbon-functions/tint.png %}

##  

+ ### **shade() function**

The shade function changes a color by mixing it with **black**.
This function also takes a **color** and **percentage** parameter to fine-tune the color mix.

```sass shade()
.shade
  background: shade(#2F7DD1, 25%)                                                              height: 100px

//  SCSS syntax

//  .shade{
      background: shade(#2F7DD1, 25%)                                                              height: 100px
//  }
```

#### Without shade()

{% img /images/bourbon-functions/no-tint.png %}

#### With shade()

{% img /images/bourbon-functions/shade.png %}

##  

+ ### **em() function**

Calculates **pixels to ems** for you.

``` sass em function in a Sass file
:q
:q
font-size: em(12)
```

``` css css output
font-size: 0.75em;
```

##  

That’s it for Bourbon for now. Over the last couple of articles we have looked in detail at mixins & functions and covered a lot of ground. I hope I could convey how awesome this project is and why it deserves a lot of respect.

Now feel free to scheme how to take over the world with it.

{% img /images/bourbon-functions/scheming.gif 700 %}

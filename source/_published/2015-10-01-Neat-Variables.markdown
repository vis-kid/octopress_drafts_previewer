---
layout: post
title: Bourbon Neat-Variables
date: 2015-10-01 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [ Sass,  Bourbon, Neat, Thoughtbot, SCSS, CSS, Design ]
---

{% img /images/Neat_01/Neat-pour.jpg %}

[{% img /images/bourbon-neat/Bourbon-Neat-Logo.png  250 450 %}](http://neat.bourbon.io/)

In this last section about Bourbon Neat we’ll look at the various “built-in” Sass variables you have at your disposal. Its gonna be a short ride but knowing how to tweak your grids is important. 

#### Variables

+ default-layout-direction
+ visual-grid-opacity
+ border-box-sizing
+ visual-grid-index
+ disable-warnings
+ visual-grid-color
+ default-feature
+ grid-columns
+ max-width
+ visual-grid
+ column
+ gutter

Before we start, I should remind you that most of the time your Neat variables are best placed in one central location like in your **_grid-settings** partial. In any case, to avoid any surprises, make sure to not forget to import these variables before you import Neat.

Sass:
``` sass
@import "bourbon/bourbon"
@import "grid-settings"
@import "neat/neat"
```

## Visualizing your grid

Let’s start with something that you should be using from day one. Neat can show you a visual grid that makes it easier to visualize your designs and better spot opportunities for experimentation. 

I’m definitely in the camp of people who advocate designing in the browser as soon as possible. No doubt, sometimes it is important so spend a little extra time in Sketch or whatever, but aiming to bridge the two faster is a reasonable ambition. Seeing the grid skelleton for your layout in the browser makes it a whole lot easier to leave other graphical design tools more and more behind.

+ ### visual-grid

By default, Neat sets **$visual-grid** to **false**. Just jump into **_grid-settings** and change it to **true**.  

Sass:
``` sass
$visual-grid: true
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-empty.png%}

[codepen example](http://codepen.io/vis-kid/pen/PPWLQK)

The color you’ll see will be a bit different—I already tweaked that a bit—but it will show you the number of columns you have set via **$grid-columns**—all within your outer containers. In this case I kept the default which is 12 columns. In general, a 12 column grid offers you a lot of flexiblity and is a solid choice for beginners as well as for advanced designers.

+ ### visual-grid-index

If you already have some content which spans the full width of the outer container(s) on the page you might be surprised to see no effect of the **$visual-grid**. In case that happens, remember that the **$visual-grid-index** is set to **back** by default. Maybe not the best default ever, but no biggie either.

SCSS:
``` scss
$visual-grid-index: back !default
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-index-back.png%}

[codepen example](http://codepen.io/vis-kid/pen/MaJRpY)

If you want to see the visual grid displayed in front of the content on the page just make this little change:

Sass:
``` sass
$visual-grid-index: front
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-index-front.png%}

[codepen example](http://codepen.io/vis-kid/pen/RWKOyB)

+ ### visual-grid-color

If you’re unhappy with the default grid color you can change that of course. Obviously its a good idea to choose a color that has good contrast compared to your design. I like using plain old **tomato**—although for the examples here I decided differently since I already used it to highlight the containers.

Sass:
``` sass
$visual-grid-color: black
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-color.png %}

[codepen example](http://codepen.io/vis-kid/pen/PPWgXL)

+ ### visual-grid-opacity

If you’re unhappy with the default opacity of 40% you can overrule that too. I believe **0.4** is a good choice for a default but every project is different. Although its not the best use of this variable, let’s see how 100% looks in the same example.

Sass:
``` sass
$visual-grid-opacity: 1
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-opacity-100.png %}

[codepen example](http://codepen.io/vis-kid/pen/pjRmPe)

+ ### grid-columns

If you think the default 12-column grid gives you not the flexibility or structure you were looking for you can change the default via this variable globally. Doing this after you already designed your way through a layout in the browser might make you face some pain tough. Take it with a grain of salt— its just an educated warning since I haven’t been there before. I mean its probably nothing tragic but manual adjustments for individual rows / columns seem like a given.

Let’s go through a couple of reasonable examples:

Sass:
``` sass
$grid-columns(6)
```

#### Screenshot:

{% img /images/bourbon-variables/grid-columns(6).png %}

[codepen example](http://codepen.io/vis-kid/pen/PPpbOo)

Sass:
``` sass
$grid-columns(16)
```

#### Screenshot:

{% img /images/bourbon-variables/grid-columns(16).png %}

[codepen example](http://codepen.io/vis-kid/pen/NGpbyN)

Sass:
``` sass
$grid-columns(4)
```

#### Screenshot:

{% img /images/bourbon-variables/grid-columns(4).png %}

[codepen example](http://codepen.io/vis-kid/pen/QjpGmG)

+ ### column

For this one we’ll have to take a step back for a minute. Neat’s grid system is based on the Golden Ratio. If that is gobbledygook to you check out my articles about Bourbon mixins where I spent a little time on that. What is important here is that you can change the relative width of a single grid column via this variable. Let’s look under the hood.

SCSS:
``` scss
$column: modular-scale(3, 1em, $golden) !default;
```

Internally it uses the **modular-scale** function which I also described in an article about Bourbon’s various functions. Here it sets up the base value of **1em** and makes every column as wide as **3** increments / steps from that base value. Additionally it locks the scale of choice to the Golden Ratio via **$golden**.  

I’ve never felt the need to tweak these things but if curiosity and some extra time for experimentation would hit me I’d start playing around with the various variables for modular scales that you have at your disposal in Bourbon. You can choose from 17 “scales” that come with the library—or make up your own of course.

SCSS
``` scss
$golden:           1.618;
$minor-second:     1.067;
$major-second:     1.125;
$minor-third:      1.2;
$major-third:      1.25;
$perfect-fourth:   1.333;
$augmented-fourth: 1.414;
$perfect-fifth:    1.5;
$minor-sixth:      1.6;
$major-sixth:      1.667;
$minor-seventh:    1.778;
$major-seventh:    1.875;
$octave:           2;
$major-tenth:      2.5;
$major-eleventh:   2.667;
$major-twelfth:    3;
$double-octave:    4;
```

#### Attention!
Make sure to import Bourbon first because the variables are defined there and not in Neat. Also, if you decide that you want to change the width of your column via a different variable for your modular scale you should not forget to change this unit in your gutters as well (more info in the next section about **gutter**).

Let’s compare the different modular scales for a default 12-column grid. Not all of them result in sensible choices but take a look and see if someting pops out that you like:


Sass:
``` sass
$column: modular-scale(3, 1em, $golden)
$gutter: modular-scale(1, 1em, $golden)
```

#### Screenshot:

{% img /images/bourbon-variables/column(golden).png %}

[codepen example](http://codepen.io/vis-kid/pen/ojZBgr)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-second)
$gutter: modular-scale(1, 1em, $minor-second)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-second).png %}

[codepen examples](http://codepen.io/vis-kid/pen/NGpdbv)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-second)
$gutter: modular-scale(1, 1em, $major-second)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-second).png %}

[codepen example](http://codepen.io/vis-kid/pen/yYMgzq)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-third)
$gutter: modular-scale(1, 1em, $minor-third)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-third).png %}

[codepen examples](http://codepen.io/vis-kid/pen/yYMgPx)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-third)
$gutter: modular-scale(1, 1em, $major-third)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-third).png %}

[codepen example](http://codepen.io/vis-kid/pen/WQpRdM)

Sass:
``` sass
$column: modular-scale(3, 1em, $perfect-fourth)
$gutter: modular-scale(1, 1em, $perfect-fourth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(perfect-fourth).png %}

[codepen example](http://codepen.io/vis-kid/pen/epvgzL)

Sass:
``` sass
$column: modular-scale(3, 1em, $augmented-fourth)
$gutter: modular-scale(1, 1em, $augmented-fourth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(augmented-fourth).png %}

[codepen example](http://codepen.io/vis-kid/pen/yYMgKR)

Sass:
``` sass
$column: modular-scale(3, 1em, $perfect-fifth)
$gutter: modular-scale(1, 1em, $perfect-fifth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(perfect-fifth).png %}

[codepen examples](http://codepen.io/vis-kid/pen/WQpRyz)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-sixth)
$gutter: modular-scale(1, 1em, $minor-sixth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-sixth).png %}

[codepen example](http://codepen.io/vis-kid/pen/GpWrXm)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-sixth)
$gutter: modular-scale(1, 1em, $major-sixth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-sixth).png %}

[codepen example](http://codepen.io/vis-kid/pen/Zbeyvm)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-seventh)
$gutter: modular-scale(1, 1em, $minor-seventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-seventh).png %}

[codepen example](http://codepen.io/vis-kid/pen/gamRvm)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-seventh)
$gutter: modular-scale(1, 1em, $major-seventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-seventh).png %}

[codepen examples](http://codepen.io/vis-kid/pen/pjewaZ)

Sass:
``` sass
$column: modular-scale(3, 1em, $octave)
$gutter: modular-scale(1, 1em, $octave)
```

#### Screenshot:

{% img /images/bourbon-variables/column(octave).png %}

[codepen example](http://codepen.io/vis-kid/pen/EVWXQz)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-tenth)
$gutter: modular-scale(1, 1em, $major-tenth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-tenth).png %}

[codepen example](http://codepen.io/vis-kid/pen/epvRMx)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-eleventh)
$gutter: modular-scale(1, 1em, $major-eleventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-eleventh).png %}

[codepen example](http://codepen.io/vis-kid/pen/JYWJvj)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-twelfth)
$gutter: modular-scale(1, 1em, $major-twelfth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-twelfth).png %}

[codepen example](http://codepen.io/vis-kid/pen/gamRzW)

Sass:
``` sass
$column: modular-scale(3, 1em, $double-octave)
$gutter: modular-scale(1, 1em, $double-octave)
```

#### Screenshot:

{% img /images/bourbon-variables/column(double-octave).png %}

[codepen example](http://codepen.io/vis-kid/pen/EVWZPG)

I can’t believe I actually took screenshots of all scales. Guess I got curious after all because I planned on only showing you a couple. Maybe not a rabbit hole you wanna follow but hey, folks like to get clever with these things. Using the default Golden Ratio is an absolute solid choice for people who just wanna move on.

+ ### gutter

Kinda the same idea as with **column**. This ones sets the relative width of a single	gutter—the space between each column—in your grid. To create a coherent grid system that has the Golden Ratio baked into every aspect, by default, **gutter** also uses **$golden** to calculate the gutters. 

SCSS:
``` scss
$gutter: modular-scale(1, 1em, $golden) !default;
```

For people new to the party, let me visualize this for you. All the **tomato** colored stuff are gutters:

{% img /images/bourbon-variables/gutter(golden).png%}

[codepen example](http://codepen.io/vis-kid/pen/zvZypr)

#### Attention!
If you decide that you want to change the width of your gutter via a different variable for your modular scale you should not forget to change this unit for your columns as well.

Sass:
``` sass
$column: modular-scale(3, 1em, $octave)
$gutter: modular-scale(1, 1em, $octave)
``` 

#### Screenshot:

{% img /images/bourbon-variables/gutter(octave).png%}

[codepen example](http://codepen.io/vis-kid/pen/EVWGEy)

+ ### max-width

This variable let’s you change the size of your outer containers that is used by the **outer-container** mixin. By default it is set to **1088** pixels which gets calculated to **em** out of the box.

#### Screenshot with default max-width of **1088px**:

{% img /images/bourbon-variables/max-width(1088).png%}

[codepen example](http://codepen.io/vis-kid/pen/dYvwjV)

Sass:
``` sass
$max-width: em(1200)
```

#### Screenshot with **1200px** max-width:

{% img /images/bourbon-variables/max-width(1200).png%}

[codepen example](http://codepen.io/vis-kid/pen/rOyoKx)

This variable is certainly handy since the “standards” for how wide web pages are supposed to span are changing rapidly and long gone are the 960px days. Through access to this variable you have one central place where you can determine how much space your content can maximally span.

+ ### default-feature

If you have gone through my previous articles you might remember that for media queries I really like the use of the **media** mixin from Neat combined with the additional convenient usage of the **new-breakpoint** function. I also mentioned that if you provide no *media feature* to your breakpoint pixel value it will default to **min-width**. Let me refresh your memeory:

Sass:
``` sass
$tablet: new-breakpoint(800px 6)

.some-responsive-element
  +media(tablet)
    +span-columns(6)
```

CSS:
``` css
@media screen and (min-width: 800px) {
  .some-responsive-element {
    ...
    ...
   }
```

As you can see, providing no *media-feature* makes things super easy if want **min-width** for setting breakpoints. If that default is messing with your Zen when dealing with media queries you can change that default as well.

Sass:
``` sass
$default-feature: max-width
```

+ ### border-box-sizing

By default this is set to **true** and I guess a good reason would be in order to mess with this one.

+ ### default-layout-direction

Through this variable your layout uses the default orientation of **LTR**—left-to-right. Obviously this can have only one of two options—**RTL** being the second. I guess if you design something for cultures that digest content from the opposite direction, this one will make you love Neat even more.

+ ### disable-warnings

If excess deprication warnings are the sort of thing that sometimes make you want to hug their authors around the neck–unlike me of course—you can mute these messages by setting this variable to **true**.

Sass:

``` sass
$disable-warnings: true
```

### Final thoughts

If you have gone through both modules about Bourbon and Neat and still don’t like the idea of using this framework for your projects I’d like it if you’d share your reasons and maybe even write about it. I’d love to hear reasonable opposing opinions or learn about better options to build lightweight semantic grids with Sass. I guess Neat is pretty hard to beat these days though. That being said, the thing that makes it so appealing to me, and that most likely will keep it relevant for quite some time, is that there isn’t much left that can be taken away to reduce it.

Btw, I just learned that thoughtbot recently announced that Bourbon and Neat are used in the new [U.S. Web Design Standards](https://playbook.cio.gov/designstandards/). Pretty darn cool!

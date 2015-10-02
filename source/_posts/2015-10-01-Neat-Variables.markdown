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

In this last section about Bourbon Neat we’ll look at the various “built-in” Sass variables you have at your disposal. It’s gonna be a short ride but knowing how to tweak your grids is important. 

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

Your variables are best placed in one central place like in your **_grid-settings** partial. In any case, to avoid any surprises, make sure to not forget to import these variables before you import Neat.

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

The color you’ll see will be a bit different—I already tweaked that abit—but it will show you the number of columns you have set via **$grid-columns** within your outer containers. In this case I kept the default which is 12 columns.

A 12 column grid offers you a lot of flexiblity to combine sections of various length neatly into it. It’s a solid choice for beginners as well as for advanced designers.

+ ### visual-grid-index

If you already have some content which spans the full width of the outer container(s) on the page you might be surprised to see no effect. In case that happens, remember that the **$visual-grid-index** is set to **back** by default. 

Sass:
``` sass
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

If you’re unhappy with the default color you can change that of course. Obviously it’s a good idea to choose a color that has good contrast compared to your design. I like using plain old **tomato**—although for the examples here I decided differently since I already used it to highlight the containers.

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

If you think the default 12-column grid gives you not the flexibility you were looking for you can change the default via this variable globally. Doing this after you already designed your way through a layout in the browser you might face some pain tough. Take it with a grain of salt— it’s just an educated warning since I haven’t been there before. I mean it’s probably nothing tragic but manual adjustments for individual rows / columns seem like a given.

Let’s through a couple of reasonable examples around:

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

For this one we’ll have to step back for a minute. Neat’s grid system is based on the Golden Ratio. If that is gobbledygook to you check out my articles about Bourbon mixins where I spent a little time on that. What is important here is that you can change the relative width of a single grid column via this variable. Let’s look under the hood.

SCSS:
``` scss
$column: modular-scale(3, 1em, $golden) !default;
```

Internally it uses the **modular-scale** function which I also described in an article about Bourbon’s various functions. Here it sets up the base value of **1em** and makes every column as wide as **3** increments from that base value. Additionally it locks the scale to be used to the Golden Ratio via **$golden**.  

I never felt the need to tweak these things but if curiosity and some extra time would hit me I would start playing around with the various scales that you have at your disposal in Bourbon. You can choose from 17 scales that come with the library—or make up your own of course.

#### Attention!
Make sure to import Bourbon first because the variables are defined there and not in Neat.

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

Let’s compare the different scales for a default 12-column grid. Not all of them result in sensible choices but take a look and see if someting pops out that you like:


Sass:
``` sass
$column: modular-scale(3, 1em, $golden)
```

#### Screenshot:

{% img /images/bourbon-variables/column(golden).png %}

[codepen example](http://codepen.io/vis-kid/pen/ojZBgr)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-second)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-second).png %}

[codepen examples](http://codepen.io/vis-kid/pen/NGpdbv)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-second)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-second).png %}

[codepen example](http://codepen.io/vis-kid/pen/yYMgzq)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-third)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-third).png %}

[codepen examples](http://codepen.io/vis-kid/pen/yYMgPx)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-third)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-third).png %}

[codepen example](http://codepen.io/vis-kid/pen/WQpRdM)

Sass:
``` sass
$column: modular-scale(3, 1em, $perfect-fourth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(perfect-fourth).png %}

[codepen example](http://codepen.io/vis-kid/pen/epvgzL)

Sass:
``` sass
$column: modular-scale(3, 1em, $augmented-fourth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(augmented-fourth).png %}

[codepen example](http://codepen.io/vis-kid/pen/yYMgKR)

Sass:
``` sass
$column: modular-scale(3, 1em, $perfect-fifth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(perfect-fifth).png %}

[codepen examples](http://codepen.io/vis-kid/pen/WQpRyz)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-sixth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-sixth).png %}

[codepen example](http://codepen.io/vis-kid/pen/GpWrXm)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-sixth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-sixth).png %}

[codepen example](http://codepen.io/vis-kid/pen/Zbeyvm)

Sass:
``` sass
$column: modular-scale(3, 1em, $minor-seventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(minor-seventh).png %}

[codepen example](http://codepen.io/vis-kid/pen/gamRvm)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-seventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-seventh).png %}

[codepen examples](http://codepen.io/vis-kid/pen/pjewaZ)

Sass:
``` sass
$column: modular-scale(3, 1em, $octave)
```

#### Screenshot:

{% img /images/bourbon-variables/column(octave).png %}

[codepen example](http://codepen.io/vis-kid/pen/EVWXQz)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-tenth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-tenth).png %}

[codepen example](http://codepen.io/vis-kid/pen/epvRMx)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-eleventh)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-eleventh).png %}

[codepen example](http://codepen.io/vis-kid/pen/JYWJvj)

Sass:
``` sass
$column: modular-scale(3, 1em, $major-twelfth)
```

#### Screenshot:

{% img /images/bourbon-variables/column(major-twelfth).png %}

[codepen example](http://codepen.io/vis-kid/pen/gamRzW)

Sass:
``` sass
$column: modular-scale(3, 1em, $double-octave)
```

#### Screenshot:

{% img /images/bourbon-variables/column(golden-octave).png %}

[codepen example](http://codepen.io/vis-kid/pen/EVWZPG)

I can’t believe I actually took screenshots of all scales. Guess I got curious because I planned on only showing you a couple. Maybe not a rabbit hole you wanna follow but hey folks like to get clever with these things. Using the default Golden Ratio is an absolute solid choice for people who just wanna move on.

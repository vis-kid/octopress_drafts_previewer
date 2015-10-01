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

In this last section about Bourbon Neat we’ll look at the various “built in” Sass variables you have at your disposal. It’s gonna be a short ride but knowing how to tweak your grids is important. 

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

Let’s start with something that you should be using from day one. Neat can show you a visual grid that makes it easier to visualize your designs and spot opportunities for experimentation. 

I’m definitely in the camp of people who advocate designing in the browser as soon as possible. No doubt, sometimes it is important so spend a little extra time in Sketch or whatever, but aiming to bridge the two faster is a reasonable ambition. Seeing the grid skelleton for your layout in the browser makes it a whole lot easier to leave other graphical design tools behind.

+ ### visual-grid

By default, Neat sets **visual-grid** to **false**. Just jump into **_grid-settings** and change it to **true**.  

Sass:
``` sass
$visual-grid: true
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-empty.png%}

[codepen example](http://codepen.io/vis-kid/pen/PPWLQK)

The color you’ll see will be a bit different—I already tweaked that abit—but it will show you the number of columns you have set via **$grid-columns**. In this case I kept the default which is 12 columns.

+ ### visual-grid-index

If you already have some content which spans the full width on the page you might be surprised to see no effect. In case that happens, remember that the **visual-grid-index** is set to **back** by default. 

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

A 12 column grid offers you a lot of flexiblity to combine sections of various length neatly into it. It’s a solid choice for beginners as well as advanced designers.

+ ### visual-grid-color

If you’re unhappy with the default color you can change that of course. Of course it’s a good idea to choose a color that has good contrast compared to your design. I like using plain old **tomato**—although for the examples here I decided differently since I already used it to highlight the containers.

Sass:
``` sass
$visual-grid-color: black
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-color.png %}

[codepen example](http://codepen.io/vis-kid/pen/PPWgXL)

+ ### visual-grid-opacity

If you’re unhappy with the default opacity of 40% you can overrule that too of course. I believe **0.4** is a good choice for a default but every project is different of course. Although its not the best use of this variable, let’s see how 100% looks in the same example.

Sass:
``` sass
$visual-grid-opacity: 1
```

#### Screenshot:

{% img /images/bourbon-variables/visual-grid-opacity-100.png %}

[codepen example](http://codepen.io/vis-kid/pen/pjRmPe)

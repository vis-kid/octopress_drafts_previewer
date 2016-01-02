---
layout: post
title: Middleman Basics 06-Podcast Site (Part 04)
date: 2016-01-02 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Hero Section

+ ### Hero Section

Why don’t we add a small hero section on top of the index site? I want something that gives us an opportunity to brand the podcast site without going full-splashy-marketing-page-apeshit. I strongly trust in “less is more” and moreover, in “don’t insult your users by bombarding them with bs”. Let’s keep it nice and simple.

In this last article we will make use of another part of the Bourbon family and implement a couple of patterns from [Refills](http://refills.bourbon.io/)—which provides a pattern / components library (styled and unstyled) and is built with Bourbon and Neat. Why reinvent the wheel when we can now and then adjust existing ones to our needs. Btw, this project is also maintained by designers at [thoughtbot](https://thoughtbot.com/)—so it’s in very good hands qualitywise.

The provided markup for the hero unit is placed in our index file—right above the part where we iterate over our ```page_articles```.

##### source/index.html.erb

``` html
...

<div class="hero">
  <div class="hero-inner">
    <a href="" class="hero-logo"><img src="https://raw.githubusercontent.com/thoughtbot/refills/master/source/images/placeholder_logo_1.png
" alt="Logo Image"></a>
    <div class="hero-copy">
      <h1>Short description of Product</h1>
      <p>A few reasons why this product is worth using, who it's for and why they need it.</p>  
    </div>
  </div>
</div>

<div class='posts'>
  <% page_articles.each_with_index do |article, i| %>
    <h2 class='post-title'><span class='post-date'><%= article.date.strftime('%b %e') %></span> <%= link_to article.title, article %></h2>

...

```

I copied the styles from the pattern section and copied it into a new file dedicated to this banner section. The provided styles are in the `.scss` syntax and so I go with the flow—see no need to convert this into `.sass` really.

##### source/stylesheets/_hero_banner.scss

``` scss

.hero {  
  $base-border-radius: 3px !default;
  $action-color: #477DCA !default;
  $large-screen: em(860) !default;
  $hero-background-top: #7F99BE; 
  $hero-background-bottom: #20392B;
  $hero-color: white;
  $gradient-angle: 10deg;
  $hero-image: 'https://raw.githubusercontent.com/thoughtbot/refills/master/source/images/mountains.png';

  @include background(url($hero-image), linear-gradient($gradient-angle, $hero-background-bottom, $hero-background-top), no-repeat $hero-background-top scroll);
  background-color: #324766;
  background-position: top;
  background-repeat: no-repeat;
  background-size: cover;
  padding-bottom: 3em;

  .hero-logo img {
    height: 4em;
    margin-bottom: 1em;
  }

  .hero-inner {
    @include outer-container;
    @include clearfix;
    margin: auto;
    padding: 3.5em;
    text-align: center;

    .hero-copy {
      text-align: center;
      
      h1 {
        color: $hero-color;
        font-size: 1.6em;
        margin-bottom: 0.5em;

        @include media($large-screen) {
          font-size: 1.8em;
        }
      }

      p {
        color: $hero-color;
        line-height: 1.4em;
        margin: 0 auto 3em auto; 

        @include media($large-screen) {
          font-size: 1.1em;
          max-width: 40%;
        }
      }
    }
  }
}


```

We will adjust this in a second but let’s take a peek first:

##### Screenshot

{% img /images/middleman/middleman_06_build/hero-unit-copy.png %}

Fits right in—that’s how I like it! Let’s tweak this to our needs by getting rid of the image and icon. But let’s start with the text.

##### source/index.html.erb

``` html

<div class="hero">
  <div class="hero-inner">
    <a href="" class="hero-logo"><img src="https://raw.githubusercontent.com/thoughtbot/refills/master/source/images/placeholder_logo_1.png
" alt="Logo Image"></a>
    <div class="hero-copy">
      <h1>MATCHA NERDZ</h1>
      <p>Podcast for green tea connoisseurs</p>  
    </div>
  </div>
</div>

```

You can tweak this any way you like. I wanna make this quick and just want to increase the size of the `h1` for both large screens and smaller devices. The hero unit header is now `2em` and `3em` respectively—not too much. The padding on `.hero-inner` also needs to move a nudge.

##### source/stylesheets

``` scss

.hero-inner {
  //padding: 3.5em;
  padding-top: 1.2em;
}

.hero-copy {
  text-align: center;
  
  h1 {
    color: $hero-color;
    font-size: 2em;
    margin-bottom: 0.5em;

    @include media($large-screen) {
      font-size: 3em;
    }
  }

```

Next let’s kill the logo placeholder. I find this things often a bit annoying.

##### source/index.html.erb

``` html

<div class="hero">
  <div class="hero-inner">
    <div class="hero-copy">
      <h1>MATCHA NERDZ</h1>
      <p>Podcast for green tea connoisseurs</p>  
    </div>
  </div>
</div>

```

That’s all we need really. Since we don’t use the hero logo, let’s get rid of their styles—dead weight.

##### source/stylesheets/_hero_banner.scss

``` scss

//.hero-logo img {
//  height: 4em;
//  margin-bottom: 1em;
//}

```

The generic background image also has to go. I’ll show you fist how I want it to look and explain then how to get there.

##### Screenshot

{% img /images/middleman/middleman_06_build/hero-unit-preview.png %}

Ignore the typography for now. You can adjust this later. I replaced the image and put a slight gradient on top of it. Since the type is white, I needed a bit more contrast for a better reading experience. If you choose an image that does not need an additional gradient, go for it!

In the SCSS blob, focus your attention to the following lines. I adjusted the Refills code with a couple of changes. First, I added an image to `source/images` and save this image in the variable `$hero-image`. Then I reuse this variable in the `background` mixin from Bourbon and reorder the image and the `linear-gradient` (a Bourbon mixin as well). Because the gradient comes first, it is overlayed on top of the ```Matcha_Nerdz.png```.

For the gradient itself, I reused our `$matcha-green` that we stored in ```source/stylesheets/base/_variables.scss```. The top color is darkend by 65 percent with a Sass function the other one is lightened by 10 percent and also made transparent via another Sass function called `rgba`. We reuse these variables then in our `background` mixin. The `gradient-angle` stayed the same. Pretty neat and easily reuseable. 

##### source/stylesheets/_hero_banner.scss

``` scss

.hero {  
  $hero-background-top: darken($matcha-green, 65%); 
  $hero-background-bottom: rgba(lighten($matcha-green, 10%), .3);
  $hero-image: '../images/Matcha_Nerdz.png';
  @include background(linear-gradient($gradient-angle, $hero-background-bottom, $hero-background-top), url($hero-image), no-repeat $hero-background-top scroll);
  margin-bottom: 2em;

  //background-color: #324766;
  //background-size: cover;

```

I also added a small margin of `2em` to push the index list down a bit. The styles I commented out for you are dead weight as well and I deleted them. 

You can play with such a gradient directly in Photoshop as well of course. Wanted to show you how you can use them in Sass. Below is a screenshot that has no linear gradient added to the hero unit. Up to you what you prefer. As a little exercise, I’ll leave the cleanup of the these styles we copied to you. If you find duplications or unused styles, I recommend you fix this before you move on.

##### Screenshot

{% img /images/middleman/middleman_06_build/hero-unit-preview-no-gradient.png %}

Time for another commit and deploy.

##### Shell

``` bash

git add --all
git commit -m 'Adds hero unit to index.html.erb
               Adds hero image with gradient
               Adds _hero_banner Sass partial
               Imports Sass partial'

middleman deploy

```

##### Screenshot

{% img /images/middleman/middleman_06_build/hero-unit-preview-no-grid.png %}

Without the visual grid, it doesn’t look you have much work left to adjust this page for your podcasting needs. A few things I’d recommend to do is find a typeface that communicates your project distinctively without being too exotic and adjust the size and spacing of your text so that it fits your hero unit background image. Since this is part of your branding I suggest you take your time and have some fun!

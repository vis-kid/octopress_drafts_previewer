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

## Topics

+ Hero Section
+ Navigation
+ Title
+ Pagination

## Hero Section

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

## Navigation

I think it’s a good time to add a navbar. We will also use a pattern from Refills and adapt for our own needs. I chose the “centered navigation” and you will find the code for it under “Patterns”. For this one we need to copy the HTML, SCSS—since we get rid of most stuff in the navbar I won’t copy the CoffeeScript code. If you need a more extensive navbar you might need that though.

I’ll start first by adding the markup to our global `layout.erb` file

### source/layouts/layout.erb

``` erb

<!doctype html>
<html>

  <head>
    <meta charset="utf-8" />
    <meta http-equiv='X-UA-Compatible' content='IE=edge;chrome=1' />
		 <meta content="IE=edge,chrome=1" http-equiv="X-UA-Compatible">
    <title>Blog Title<%= ' - ' + current_article.title unless current_article.nil? %></title>
    <%= feed_tag :atom, "#{blog.options.prefix.to_s}/feed.xml", title: "Atom Feed" %>
    <%= stylesheet_link_tag "all" %>
    <%= javascript_include_tag  "all" %>
  </head>

  <body>

    <header class="centered-navigation" role="banner">
      <div class="centered-navigation-wrapper">
        <a href="javascript:void(0)" class="mobile-logo">
          <img src="https://raw.githubusercontent.com/thoughtbot/refills/master/source/images/placeholder_logo_3_dark.png" alt="Logo image">
        </a>
        <a href="javascript:void(0)" id="js-centered-navigation-mobile-menu" class="centered-navigation-mobile-menu">MENU</a>
        <nav role="navigation">
          <ul id="js-centered-navigation-menu" class="centered-navigation-menu show">
            <li class="nav-link"><a href="javascript:void(0)">Products</a></li>
            <li class="nav-link"><a href="javascript:void(0)">About Us</a></li>
            <li class="nav-link"><a href="javascript:void(0)">Contact</a></li>
            <li class="nav-link logo">
              <a href="javascript:void(0)" class="logo">
                <img src="https://raw.githubusercontent.com/thoughtbot/refills/master/source/images/placeholder_logo_3_dark.png" alt="Logo image">
              </a>
            </li>
            <li class="nav-link"><a href="javascript:void(0)">Testimonials</a></li>
            <li class="nav-link more"><a href="javascript:void(0)">More</a>
              <ul class="submenu">
                <li><a href="javascript:void(0)">Submenu Item</a></li>
                <li><a href="javascript:void(0)">Another Item</a></li>
                <li class="more"><a href="javascript:void(0)">Item with submenu</a>
                  <ul class="submenu">
                    <li><a href="javascript:void(0)">Sub-submenu Item</a></li>
                    <li><a href="javascript:void(0)">Another Item</a></li>
                  </ul>
                </li>
                <li class="more"><a href="javascript:void(0)">Another submenu</a>
                  <ul class="submenu">
                    <li><a href="javascript:void(0)">Sub-submenu</a></li>
                    <li><a href="javascript:void(0)">An Item</a></li>
                  </ul>
                </li>
              </ul>
            </li>
            <li class="nav-link"><a href="javascript:void(0)">Sign up</a></li>
          </ul>
        </nav>
      </div>
    </header>
    
    <div id="main" role="main">
      <%= yield %>
    </div>

    <%= partial "partials/footer" %>
    
  </body>
</html>

```

Whoa! That’s quite a chunk of code. Are you thinking the same as me? This looks nasty, right? Let’s put this into a partial.

##### source/layouts/layout.erb

``` erb

...

  <body>

    <%= partial "partials/navbar" %>
    
    <div id="main" role="main">
      <%= yield %>
    </div>

    <%= partial "partials/footer" %>
    
  </body>
</html>

```

##### source/partials/_navbar.erb

``` erb

<header class="centered-navigation" role="banner">
  <div class="centered-navigation-wrapper">
    <a href="javascript:void(0)" class="mobile-logo">
      <%= image_tag "matcha_nerdz_logo.png", alt: "Logo image" %>
    </a>
    <a href="javascript:void(0)" id="js-centered-navigation-mobile-menu" class="centered-navigation-mobile-menu">MENU</a>
    <nav role="navigation">
      <ul id="js-centered-navigation-menu" class="centered-navigation-menu show">
        <li class="nav-link"><%= link_to 'Home', '/matcha-nerdz' %></li>
        <li class="nav-link logo">
          <%= image_tag "matcha_nerdz_logo.png", alt: "Logo image" %>
        </li>
        <li class="nav-link"><%= link_to 'About', '/pages/about.html' %></li>
      </ul>
    </nav>
  </div>
</header>

```

I’ve removed a bunch of stuff that I don’t need and only end up with my logo that I stored in `/images` and two links for home and about pages. For the two links I used the ```link_to``` helper method. It takes two arguments: The string you want users to click on and the location you want to link to. I’m sure peope who have played a bit with Rails or Sinatra are familiar with this. Handy, but no big deal. The link for “Home” (/matcha-nerd) will break for your local host but it is working on GitHub pages where we need the namespace. 

The avid reader might also have discovered that our about link links to a simple HTML page that I placed in a new directory named `pages`. I suggest you put HTML pages like contact, faq or whatever also in this directory. If you put these static pages in there you should have no problems customizing them to your needs. Just have some fun and apply what you’ve learned so far with these pages. From here on you are on your own with these but you now know everything you need. Samo, samo!

A word about the image tags and the asset path on GitHub Pages. I decided to replace the plan `img` tags for the logo with a Middleman helper called ```image_tag```. It’s not only pretty concise and readable but also fixes an issue you will run into with the `img` tag when you build the site and deploy it to GitHub Pages. The url for the asset link on individual articles gets all screwed up and this is the simplest solution to fix that.

``` html

<img src="/images/matcha_nerdz_logo.png" alt="Logo image"> 

```

Before using ```image_tag```, the url for the logo looked like this: ```http://your_username.github.io/images/matcha_nerdz_logo-hash_numbers.png```

``` erb

<%= image_tag "matcha_nerdz_logo.png", alt: "Logo image" %>

```

This Middleman helper provided the url with the app name `matcha-nerdz`—it correctly namespaced the asset and gives GitHub Pages access to this image file (```http://your_username.github.io/matcha-nerdz/images/matcha_nerdz_logo-hash_numbers.png```).

The styles I copied from Refills are in a new Sass partial of course.

##### source/stylesheets/all.sass

``` sass

@import 'header_navbar'

```

##### source/stylesheets/_header_navbar.scss

``` scss

.centered-navigation {
  $base-border-radius: 3px !default;
  $dark-gray: #333 !default;
  $large-screen: em(860) !default;
  $base-font-color: white;
  $centered-navigation-padding: 1em;
  $centered-navigation-logo-height: 2em;
  $centered-navigation-background: #E7F1EC;
  $centered-navigation-color: $base-font-color;
  $centered-navigation-color-hover: $text-color;
  $centered-navigation-height: 60px;
  $centered-navigation-item-padding: 1em;
  $centered-navigation-submenu-padding: 1em;
  $centered-navigation-item-nudge: 2.2em;
  $horizontal-bar-mode: $large-screen;
  background-color: $matcha-green;
  border-bottom: 1px solid darken($matcha-green, 5%);
  min-height: $centered-navigation-height;
  width: 100%;
  z-index: 9999;

  // Mobile view

  .mobile-logo {
    display: inline;
    float: left;
    max-height: $centered-navigation-height;
    padding-left: $centered-navigation-padding;

    img {
      max-height: $centered-navigation-height;
      padding: .8em 0;
    }

    @include media($horizontal-bar-mode) {
      display: none;
    }
  }

  .centered-navigation-mobile-menu {
    color: $centered-navigation-color;
    display: block;
    float: right;
    line-height: $centered-navigation-height;
    margin: 0;
    padding-right: $centered-navigation-submenu-padding;
    text-decoration: none;
    text-transform: uppercase;

    @include media ($horizontal-bar-mode) {
      display: none;
    }

    &:focus,
    &:hover {
      color: $centered-navigation-color-hover;
    }
  }

  // Nav menu

  .centered-navigation-wrapper {
    @include outer-container;
    @include clearfix;
    position: relative;
    z-index: 999;
  }

  ul.centered-navigation-menu {
    -webkit-transform-style: preserve-3d; // stop webkit flicker
    clear: both;
    display: none;
    margin: 0 auto;
    overflow: visible;
    padding: 0;
    width: 100%;
    z-index: 99999;

    &.show {
      display: block;
    }

    @include media ($horizontal-bar-mode) {
      display: block;
      text-align: center;
    }
  }

  // The nav items

  .nav-link:first-child {
    @include media($horizontal-bar-mode) {
      margin-left: $centered-navigation-item-nudge;
      padding-right: 0px;
    }
  }

  ul li.nav-link {
    background: lighten($matcha-green, 8%);
    display: block;
    line-height: $centered-navigation-height;
    overflow: hidden;
    padding-right: $centered-navigation-submenu-padding;
    text-align: right;
    width: 100%;
    z-index: 9999;

    a {
      color: $centered-navigation-color;
      display: inline-block;
      outline: none;
      text-decoration: none;

      &:focus,
      &:hover {
        color: $centered-navigation-color-hover;
      }
    }

    @include media($horizontal-bar-mode) {
      background: transparent;
      display: inline;
      line-height: $centered-navigation-height;

      a {
        padding-right: $centered-navigation-item-padding;
      }
    }
  }

  li.logo.nav-link {
    display: none;
    line-height: 0;

    @include media($large-screen) {
      display: inline;
    }
  }

  .logo img {
    margin-bottom: -$centered-navigation-logo-height / 3;
    max-height: $centered-navigation-logo-height;
  }
}

```

Because I deleted a bunch of stuff I didn’t need from the navbar markup—like the submenu—I was able to get rid of a significant chunk of the relevant styles in this file. Since I don’t know if you need a more elaborate navbar and wanna take the code right from these examples I suggest you copy the original code if you have bigger plans for the navbar. Play with the Sass to fit your style, remove dead code and duplications. I simply adjusted the background color and link colors, played with the transparency of the logo, changed the border and moved on. Have fun and go crazy if you like. I just wanted to use a super simple navbar with the brand color and a centered logo. Turned out pretty good for this little work I’d say.

##### Screenshot Index Page

{% img /images/middleman/middleman_06_build/navbar-screenshot.png %}

##### Screenshot Detail Page

{% img /images/middleman/middleman_06_build/detail-page-with-navbar.png %}

Time to package this into a git commit and for deployment:

##### Shell

``` bash

git add --all
git commit -m 'Implements a header with navbar
               Adds header partial to layout
               Takes care of deployed asset url for logo
               Imports Sass partial for navbar
               Adds logo
               Adjusts Refills styles
               Adjusts Refills markup'

middleman deploy

```

## Title

The next change is just a small one, just a touch really. We need to update the title tag in our layout.

##### source/layouts/layout.erb

``` erb

<title>Matcha Nerdz<%= ' - ' + current_article.title unless current_article.nil? %></title>

```

This gives us a dynamic title that always starts with our site’s name and attaches the article’s title if available.

##### Shell

``` bash

git add --all
git commit -m 'Adjusts site’s title'

middleman deploy

```

## Pagination

When you look at the bottom of the index list of articles you’ll see something essential missing—navigating our list of posts.

##### Screenshot

{% img /images/middleman/middleman_06_build/index-list-no-pagination.png %}

I’m not a fan of overly clever pagination links—bulky ones are also not winning any awards with me. Let’s keep it simple and provide two links for next and previous pages. Middleman makes this incredibly convenient. We just need to adjust our ```config.rb``` and tell the frontmatter of our index page to fine tune it.

##### config.rb

``` ruby

blog.paginate = true

```

First uncomment the line above. After that you just tell the index page how many articles you want to see. I think 10 posts per page are enough.

##### source/index.html.erb

``` erb

---

per_page: 10
pageable: true

---

```

The final step before we apply some styling is to place both links conveniently at the bottom of the list. First we need to get rid of these lines of code below that I commented out. They were placed at the very top of your index page.

##### source/index.html.erb

``` html
<!--
<% if paginate && num_pages > 1 %>
  <p>Page <%= page_number %> of <%= num_pages %></p>

  <% if prev_page %>
    <p><%= link_to 'Previous page', prev_page %></p>
  <% end %>
<% end %>
-->

```
And then place this at the very bottom of this page.

``` erb

<% if paginate %>

  <% if prev_page %>
    <p class='pagination-link'><%= link_to '<< Previous page', prev_page %></p>
  <% end %>

  <% if next_page %>
    <p class='pagination-link'><%= link_to 'Next page >>', next_page %></p>
  <% end %>

<% end %>

```

This gives us the navigational links we need side by side and supplies us with a class to tweaks a few things. I decided to go with a partial for the Sass code because it didn’t fit either in the footer or the index article styles—and it deserves one of its own, especially should it grow more in size.

##### source/stylesheets/all.sass

``` sass

@import 'pagination'

```

##### source/stylesheets/_pagination.sass

``` sass

.pagination-link
  +shift(2)
  margin-bottom: 4em
  &:first-of-type
    float: left
    margin-right: 4em
  a
    +transition(color 0.25s ease-in-out)
    color: $text-color
    font-size: 1.1em
    &:hover
      color: $matcha-green

```

It’s nothing tricky I think, we shift it a bit to the right, arrange the links to float next to each other—default would be block behaviour being stacked on top of each other—and applied a little transitional effect when the user hovers over the link. That’s all we need right now. Let’s have a look.

##### Screenshot: Next Page

{% img /images/middleman/middleman_06_build/index-pagination-next.png %}

##### Screenshot: Previous Page / Next Page

{% img /images/middleman/middleman_06_build/index-pagination-next-previous.png %}

Alrighty, time for another commit.

##### Shell

```bash

git add -all
git commit -m 'Adds Pagination to index list of posts
               Adjusts config.rb
               Adjusts markup on index page
               Adds styles in _pagination Sass partial'

middleman deploy

```

## Final Thoughts

I guess that should suffice for version 01—also, I ran out of space a little over the last six articles. As a next step, you should play with media queries to make the layout responsive to various screen sizes. Also, should you decide to do a podcast for real, I can only congratulate you. It’s a great way to learn from experts and also to increase your network significantly. Doing something of value for the community is always a good idea and tends to pay off big time. One last tip, try to learn by doing and experiement as much as you can! Reading alone just doesn’t cut it—been there, done that! If you like to share the lessions learned by writing about it, you will deepen even more your understanding of the topic. Have fun!

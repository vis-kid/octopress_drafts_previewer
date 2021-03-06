---
layout: post
title: Middleman Basics 03-Podcast Site (Part 01)
date: 2015-11-22 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Roadmap
+ Basic Blog Setup
+ LiveReload
+ Organizing Posts
+ GitHub Pages Deployment
+ Smarter Assets
+ Bourbon Setup
+ Normalize & jQuery

+ ### Roadmap

Let’s start with a little heads up where this is going. Over the next couple of articles I’m gonna build a small static site for a fictitious podcast called “Matcha Nerdz”—a podcast for people who wanna dive into all things powdered green tea. It will have the following pages:

+ A page for each tag
+ A detail page for every episode
+ An index page for previous podcasts

We will use Middleman for generating the static site and the Bourbon suite for all things styling. I expect / hope that you have taken a look at my articles about Bourbon, Neat and Middleman before you came here. If not, see you in a bit—unless you feel you are already prepared enough of course. I will explain what’s going on along the way but I will not cover the basics as I did previously.

For all things styling, I heavily rely on Bourbon for quite a while. Also I really dig the indented Sass syntax—I never really got why people are keen to write all that extra junk with the **.scss** syntax. The **.sass** syntax is the only (probably) unfamiliar bit I wanna throw at newbies because it’s really worth it and I’d kick myself writing it “sassyly”. Originally I wanted to use Slim instead of HTML and ERB but I decided against that since I don’t want to introduce too many unknowns for newbies. I want to do something similar with Slim though in the future—mainly because I feel it’s the best solution to write concise and intelligent markup out there at the moment. So raincheck. Why not Haml? Simple, Slim is more awesome!

+ ### Basic Blog Setup

Let’s initiate a new app for our podcast site:

**Shell:**

``` bash
middleman init matcha_nerdz
```
and of course

``` bash
cd matcha_nerdz
```

**Git:**

``` bash
git init      
# => to initiate new Git repo

git add --all 
# => adds all the files for staging

git commit -m 'Initital commit' 
# => commits changes

```

Now we add the blog template to the mix. The blog template is a good basis for our podcast site. Later we will adjust the articles to display podcast audio tracks from SoundCloud. For now, it’s just a blog though:

**Gemfile**

``` ruby
gem "middleman-blog"
```

**Shell**

``` bash
bundle
# or
bundle exec middleman
```

``` bash
middleman init --template=blog
```

This will update your **matcha_nerdz** folder. **.config.rb** and your index template get a little update as well. On top of that you get new templates for your feed, tags page, calendar page, an example article and a new layout. Check the output from the terminal:

``` bash
identical  .gitignore
   update  config.rb
    exist  source
   create  source/2012-01-01-example-article.html.markdown
   create  source/calendar.html.erb
   create  source/feed.xml.builder
   update  source/index.html.erb
   create  source/layout.erb
   create  source/tag.html.erb
    exist  source/stylesheets
    exist  source/javascripts
    exist  source/images
```

**Git:**

``` bash
git add --all
git commit -m 'Adds blog template'
```

What you can do now is create new articles via the command line. Pretty handy if you ask me if you can spare yourself typing all these dates and stuff.

**Shell**

``` bash
middleman article 'My new fancy second article'
#=> create  source/2015-11-22-my-wonderful-second-article.html.markdown
```

This creates a new markdown article under **/source**. Not optimal storage-wise but we’ll get there. Fire up your server to see your first example blog article:

**Shell**

``` bash 
middleman
#or
middleman server
```

Next we need have some housekeeping to do. The blog template created a new layout under **source/layout.erb**. We wanna delete the orginal one in **source/layouts/layout.erb** and move the new one in there:

**Shell:**

``` bash
rm source/layouts/layout.erb
mv source/layout.erb source/layouts/
```

We also need to update the new **layout.erb** with stuff that was deleted in the layout file. Add this to your **head** tag:

**source/layouts/layout.erb**

``` erb
<meta content="IE=edge,chrome=1" http-equiv="X-UA-Compatible">
<%= stylesheet_link_tag "normalize", "all" %>
<%= javascript_include_tag  "all" %>
```

Most importantly, this makes sure that your JS and style assets are avaiable.

**Git**

``` bash
git rm source/layout.erb
git add --all
git commit -m 'Moves new layout into /layouts
               Adds asset links
               Removes old layout'
```

### LiveReload

To make our lives a tad more convenient we’ll add LiveReload to the mix. We need the gem and then activate it in your **config.rb** file:

**Gemfile**

``` ruby
gem 'middleman-livereload'
```

**Shell**

``` bash
bundle
``` 

**config.rb**

``` ruby
#uncomment
activate :livereload
```

**Git**

``` bash
git add --all
git commit -m 'Activates LiveReload'
```

With this activated, restart your server and your page refreshes automatically whenever you change content on the page, styles or behaviour. Life saver, trust me!

### Attention!
Word of caution. If you have another local server running, LiveReload may not play ball. So you’d need to kill that other server for now. Weird bug to hunt down if you don’t expect ports being already taken. Also, sometimes killing the Wi-Fi and restarting the server solved local host issues. But to be honest I don’t remember if I ran into that issue in the past with Middleman or Jekyll. Anyway, keep it in mind.

### Organizing Posts

When you look where articles are stored right now, you’ll quickly realize that this organization directly under **/source** becomes very quickly very tedious. Couple of articles and you drown in posts. No need to be that messy. What we can do is create a dirctory under /source for all your posts, move your article(s) in there and let Middleman know where to find them. 


**Shell**

``` bash
mkdir source/posts
mv source/2012-01-01-example-article.html.markdown source/posts/
```
Then we add **/posts** as a source for your blog articles:

**config.rb**

``` ruby
blog.sources = "posts/:year-:title.html"
```

**Git**

``` bash
git rm source/2012-01-01-example-article.html.markdown
# Removes moved file from repo

git add --all
gco -m 'Adds new folder for posts and adds source in config.rb'
```

That’s it. Nothing should have changed and you should see the example article as before. Storage of posts however is a lot more sane. What’s also cool is that if you create new articles via the command line, your new posts will get stored in **/post** automatically. Awesome!

**Shell**

``` bash
middleman article 'My awesome 3rd article'
# => create  source/posts/2015-my-awesome-3rd-article.html.markdown

```

### GitHub Pages Deployment

For me, pushing static sites to GitHub Pages is such a convenient solution that I don’t wanna put you through deploying via Heroku or Amazon’s S3 service. Let’s keep it simple!

**Gemfile**

``` ruby

gem "middleman-deploy"

```

**Shell**

``` bash

bundle

```

We need to add a deploy block to **config.rb**:

``` ruby

activate :deploy do |deploy|
  deploy.method = :git
  deploy.branch = 'gh-pages'
  deploy.build_before = true
end

```

In order for GitHub Pages to find your CSS and JS assets you need to activate the following in **config.rb** as well:

``` ruby

configure :build do
  activate :relative_assets
end

```


Then create a repo on GitHub, add the remote and deploy:

**Shell**

``` bash

git remote add origin https://github.com/yourusername/repositoryname.git

middleman deploy

```

Boom! Your site is live under `yourusername.github.io/projectname` and your assets should be sorted out. I love this process—couldn’t be easier and more user friendly. Great job GitHub!

**Git**

``` bash

git add --all
gco -m 'Adds setup for GitHub Pages deployment'

```

### Smarter Assets

In the last step before we get into the Bourbon setup, I’d like to get rid of the styles that come with Middleman and optimize the assets for a better performance in the browser—asset minification and concatenation. Go to **source/stylesheets/all.css** and delete it’s content. Just keep the first line:

``` css
@charset "utf-8";
```

**Git**

``` bash

git add --all
git commit -m 'Removes unneccessary Middleman styles'

```

Next we want to activate a couple of options to optimize for performance in **config.rb**:

``` ruby

configure :build do
  activate :asset_hash
  activate :minify_javascript
  activate :css
  activate :gzip
end

```

**Git**

``` bash

git add --all
git commit -m 'Activates performance optimizations'

```

Let me quickly explain what we did here:

+ **:gzip**

At the moment, gzip is the most popular and effective compression method. It’s compression algorithm finds similar strings within a file and compresses them. For HTML, which is full of white space and matching tags, this is very effective and adds up to reducing the HTTP response size by a whopping 70%. Activating this, not only gzips your HTML, but also CSS and JSS files. During build, Middleman creates your files as usual but also duplicates them with a **.gz** version. When a browser gets in touch with your files, it can choose if it can serve gzip compressed files or regular ones. gzipping is supported heavily by web and mobile browsers. 
 
+ **:minify_css**

This process strips out all unneccessary junk from your styles and reduces their file size significantly. Bascially, your CSS becomes one big blob—optimized for being read by a machine. Definitely not friendly on the eyes.

+ **:minify_javascript**

Same as **minify_css** but a bit more involved and sophisticated.

+ **:asset_hash**

This activates hashing of your assets. It means that your asset filenames change and receive some extra information—during the build process—that informs browsers if they need to re-download assets or not. Now, the name of a file is dependent on the contents of that file. Hashed assets get cached by browsers and your sites get rendered faster. Another word for this is “fingerprinting” because it provides a simple solution to inform browsers whether or not two versions of a file are indentical. The deployment date does not matter—only the contents. Take a look below how hashed assets’ files look like: 

**Screenshots**

{% img /images/middleman/basics_03_build/asset_hash_css.png %}

{% img /images/middleman/basics_03_build/asset_hash_images.png %}

{% img /images/middleman/basics_03_build/asset_hash_js.png %}

Looks nasty, but now your images, stylesheets and JavaScript files get a unique name through this added “random” code—what is called a (unique) hash. Every time you change an asset and go through the build process again, this hash changes which in turn signals to browsers that then, and only then, they need to re-download that particular file. The files then sorta expired. This is called “cache busting”. Btw, you can refer to your files the same way as before but during build the references in your HTML and what not get updated to use these hashed names. Take a look:

**/build/index.html(.gz)**

``` html

<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv='X-UA-Compatible' content='IE=edge;chrome=1' />
		 <meta content="IE=edge,chrome=1" http-equiv="X-UA-Compatible">
    <title>Blog Title</title>
    <link rel="alternate" type="application/atom+xml" title="Atom Feed" href="/feed.xml" />
    <link href="stylesheets/normalize-6197e73d.css" rel="stylesheet" type="text/css" /><link href="stylesheets/all-0355b587.css" rel="stylesheet" type="text/css" />
    <script src="javascripts/all-da39a3ee.js" type="text/javascript"></script>
  </head>
  <body>

  ...

```

See, in your **/build** folder, JS and CSS files get referenced with the hashed asset names automatically. As a result of this hashing business, when you go through different pages in the same session or revisit a page again later, these assets have been cached and don’t need to be requested again—only until you change something. This process cuts down your number of requests by a staggering amount. Isn’t that cool? All of that with one line of code in **config.rb** and some *Sprockets* wizardry. Booyakasha!

The key with all these asset optimization techniques is to minimize the number of requests and the request size of your files / assets. Middleman offers great performance boosts right out the box without any work on your end really. Just activate this stuff and sleep tight. GitHub Pages has everthing gzipped and minified out of the box btw. Doesn’t hurt though to make sure everything is in place—especially if you later decide to host your app somewhere else. By then, you don’t need to think about solving this problem again. 

Let’s have a look where we’re at. Your index page should look pretty barebones now: 

**Screenshot**

{% img /images/middleman/basics_03_build/matchanerdz_screen_01.png %}

+ ### Bourbon Setup

For this project I want to use three gems from Bourbon:

+ Bourbon
+ Neat
+ Bitters

Let’s add them to our **Gemfile** and bundle:

``` ruby

gem 'bourbon'
gem 'neat'
gem 'bitters'

```

**Shell**

``` bash

bundle

```

Bourbon and Neat are now good to go (almost). Bitters needs to install a few things first though. You need to change into your stylesheets directory and activate a generator that places a bunch of Bitters files in a **/base** folder.

**Shell**

``` bash

cd source/styleheets
bitters install

```

Take a look what we got after this:

**Screenshot**

{% img /images/middleman/basics_03_build/install_bitters_base.png %}

Bitters is something like a baseline for your designs. It gives you a couple of sane designs for stuff like buttons, type, forms, error messages and so on. Bitters also prepared a **grid-settings** file for your **Neat** grid which we also have to set up by uncommenting the following line in **source/stylesheets/base/_base.scss**:

``` scss
@import "grid-settings";
```

To complete our Bourbon settings for now I’d like to add the following variables to our grid-settings. They lay the groundwork for sizing our grid and activate a visual grid that helps us to better align our design.

**/source/stylesheets/base/_grid-settings.scss**

``` scss

$column: 90px;
$gutter: 30px;
$grid-columns: 12;
$max-width: 1200px;

$visual-grid: true;
$visual-grid-index: back;
$visual-grid-opacity: 0.15;
$visual-grid-color: red;

```

The final step to make this work is rename **/stylesheets/all.css** to **/stylesheets.all.sass** and import our Bourbon files.(Since we switched to the indented Sass syntax, we also need to kill the semicolon at the end of the **@charset** line.)

**all.css.scss**

``` sass
@charset "utf-8"

@import 'bourbon'
@import 'base/base'
@import 'neat'

```

We import Bitters’ base file here right after Bourbon because we need access to Neat’s **grid-settings** file—which is in the **/base** folder—before we import Neat.

**Git**

``` bash

git add --all
git commit -m 'Sets up Bourbon and activates grid settings'
```

Let’s have a look! You can see the red visual grid and also that thanks to Bitters, our typography already improved a bit beyond browser defaults.

**Screenshot**

{% img /images/middleman/basics_03_build/bourbon_installed_visual_grid.png %}

+ ### Normalize & jQuery

Let’s get this out of the way also, shall we? Middleman comes with a [Normalize](https://necolas.github.io/normalize.css/) file which gets not imported into **all.css** by default. That’s one unneccessary asset request we can easily get rid of. Rename **source/stylesheets/normalize.css** to **source/stylesheets/_normalize.css.scss** first. Now we have a partial that we need to import right at the top after **@charset** in **source/stylesheets/all.sass**:

``` sass

@charset "utf-8"

@import 'normalize'

@import 'bourbon'
@import 'base/base'
@import 'neat'
@import 'normalize'

```

In case you’re wondering about what *Normalize* does, think of it as leveling the playing field between the default styles that browsers like to add. One browser likes to add padding here, another has useless margins there, etc. Projects like Normalize try to reset that behaviour so that all elements / designs get rendered more consistently across browsers. 

One thing we shouldn’t overlook is the link for our stylesheets in our layout. Since we’re using Sass partials that all get imported into a final, “global” stylesheet, we do not need a link to **normalize.css** anymore—a link to **all.sass** is enough:

**source/layouts/layout.erb**

``` erb

<%= stylesheet_link_tag "all" %>

```

**Git**

``` bash

git rm source/stylesheets/normalize.css
git add --all
git commit -m 'Imports normalize partial properly'

```

Finally, before we take a break, we need to add jQuery which we’ll need later on.

**Gemfile**

``` ruby
gem "jquery-middleman"
```

**Shell**

``` bash
bundle

```

Since I wanna use CoffeeScript for this project, we need to rename **source/javascripts/all.js** to **source/javascripts/all.coffee**. In there we require jQuery for Sprockets / Asset Pipeline and we’re all set.

**all.coffee**

``` javascript
//= require jquery
```

**Git**

``` bash
git rm source/javascripts/all.js
git add -all
git commit 'Adds jQuery to the Mix
            Renames gobal js file to coffee'
```

**Shell**

``` bash
middleman deploy
```


After deploying, open your site on GitHub Pages to see if everyting works as expected. Nice job!

+ ### Break

Let’s take a break. We got quite a few boring setup steps out of the way with this one. Hope you got a clear picture what you need for a solid basis when you start a new Middleman project. Next we’ll expand on what we’ve built here and continue working towards a decent site for our podcast. 

---
layout: post
title: Middleman Basics 03-Podcast Site
date: 2015-11-22 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Roadmap
+ Basic Setup
+ Smarter Assets

+ ### Roadmap

Let’s start with a little heads up where this is going. Over the next two articles I’m gonna build a small static site for a ficticious podcast called “Matcha Nerdz” where people dive into all things powdered green tea. It will have the follwing pages:

+ A calendar page
+ A page for each tag
+ A detail page for every episode
+ An index page for previous podcasts

We will use Middleman for generating the static site and the Bourbon suite for all things styling. I expect (hope) that you have taken a look at my articles about Bourbon, Neat and Middleman before you came here. If not, see you in a bit—unless you feel you are already prepared enough of course. I will explain what’s going on along the way but I will not cover the basics as I did previously.

For all things styling, I heavily rely on Bourbon. Also I really dig the indented Sass syntax—I never really got why people are keen to write all that extra junk with the **.scss** syntax. The **.sass** syntax is the only (probably) unfamiliar bit I wanna throw at newbies because its really worth it and I’d kick myself writing it “sassy-ly”. Originally I wanted to use Slim instead of HTML and ERB but I decided against that since I don’t want to introduce too many unknowns for newbies. I want to do something similar with Slim though in the future—mainly because I feel it is the best solution to write concise and intelligent markup out there at the moment. So raincheck. Why not Haml? Simple, Slim is more awesome!

+ ### Basic Setup

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

### Blog Basis

Now we add the blog template to the mix. The blog template is a good basis for our podcast site. Later we will adjust the articles to display your podcast audio tracks. For now, its just a blog though:

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

This will update your **matcha_nerdz** folder. **config.rb** and your index template get updated. On top of that you get new templates for your feed, tags page, calendar page, an example article and a new layout. Check the output from the terminal:

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

This creates a new markdown article under source. Not optimal yet storage wise but we’ll get there.

Fire up your server to see your first example blog article:

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

We also need to update the new **layout.erb** with stuff that was in the deleted layout file. Add this to your **head** tag:

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
Word of caution. If you have another local server running LiveReload may not play ball. So you’d need to kill that server for now. Weird bug to hunt down if you don’t expect this.

### Organizing Posts

When you look where articles are stored right now, you’ll quickly realize that this organization directly under **/source** becomes very quickly very tedious. After the third article that is stored there things get messy. What we can do is create a dirctory under /source for all your posts, move your article(s) in there and let Middleman know where to find them. 


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

That’s it. Nothing should have changed and you should see the example article as before. They are just stored a bit more sane. What’s also cool is that if you create new articles via the command line, your new posts will get stored in **/post** automatically. Awesome!

**Shell**

``` bash
middleman article 'My awesome 3rd article'
# => create  source/posts/2015-my-awesome-3rd-article.html.markdown

```

### Deployment

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

Then create the repo on GitHub and add the remote:

**Shell**

``` bash

git remote add origin https://github.com/yourusername/repositoryname.git

middleman deploy

```

Boom! Your site is live under `yourusername.github.io/projectname`. I love this process—couldn’t be easier and more user friendly. Great job GitHub!

**Git**

``` bash

git add --all
gco -m 'Adds setup for GitHub Pages deployment'

```

### CSS Cleanup

In the last step before we get into the Bourbon setup, I’d like to get rid of the styles that come with Middleman. Go to **source/stylesheets/all.css** and delete its content. Just keep the first line:

``` css
@charset "utf-8";
```

**Git**

``` bash
git add --all
git commit -m 'Removes unneccessary Middleman styles'
```

Your index page should look pretty barebones now. 

**Screenshot**

{% img /images/middleman/basics_03_build/matchanerdz_screen_01.png %}

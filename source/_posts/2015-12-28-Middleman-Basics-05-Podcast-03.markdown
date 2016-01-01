---
layout: post
title: Middleman Basics 05-Podcast Site (Part 03)
date: 2015-12-28 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Posts Detail Page
+ Style Duplications
+ Relative Links
+ Index List Player
+ Why SoundCloud? (Optional)

+ ### Posts Detail Page

I think we should shift our attention and give our details page a little bit of basic love before we adjust the app to display our podcast episodes. We are not entirely done with the index page, if we have some room left in this tutorial I’ll probably add a couple of media queries to deal with different screen resolutions. For now I’d say let’s move on though. What’s the status quo?

##### Screenshot

{% img /images/middleman/middleman_05_build/detail_page_initial_state.png %}

Yikes, doesn’t look too good! Our text is all over the place. Let’s fix that one first. Think it’s a good idea to activate the visual grid again.

##### source/stylesheets/base/_grid-settings.scss

``` scss

$visual-grid: true; 

```

We need to create a separate layout for our detail pages for our posts. The layout will be flexible so that you can use them for pure blog posts and for posting podcast episodes as well—a little conditional will help us out with that. More on that later though. Let’s open `config.rb` and add this line. This will tell Middleman that we have a separate layout for detail pages, that it can be found in `layouts/blog-layout`. 

##### config.rb

``` ruby

activate :blog do |blog|
  blog.layout = "layouts/blog-layout"
end

```

Next we need to create `layouts/blog-layout.erb`. Remember that the `.erb` is necessary without the `.html` extension to make this work properly.

##### layouts/blog-layout.erb

``` erb

<% wrap_layout :layout do %>

  <article class='article-detail'>

    <h2 class='detail-post-title'><%= current_article.title %></h2>
    <h3 class='detail-post-date'><%= current_article.date.strftime('%b %e') %></h3>
  
    <% if current_page.data.soundcloud_id %>
      <section class='soundclould-player-big'>
        <iframe 
          width="100%" 
          height="450" 
          scrolling="no" 
          frameborder="no" 
          src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/<%= current_page.data.soundcloud_id %>&amp;auto_play=false&amp;hide_related=false&amp;show_comments=true&amp;show_user=true&amp;show_reposts=false&amp;visual=true">
        </iframe>
      </section>
    <% end %>
  
    <%= current_article.body %>
  
  </article>

<% end %>

```

So let’s talk about what’s going on here. First of all, we need to wrap this `blog-layout` inside our general `layout`. 

``` erb

<% wrap_layout :layout do %>

  ...

<% end %>

```

Why? Simple, because we want to reuse a lot of the stuff from the original layout and not duplicate things like the `footer` partial or the asset links in `head`. I’m sure newbies will wrap their head around this quickly. Just give it a minute or two to sink in if this looks weird to you at first. The layout we used previously was more of a global thing. Now we need a bit more specificity to fit our needs.

Inside the `article` container we manually build what we need to template our post. It has a title, a date, an optional SoundCloud embedded widget and of course the article body itself. Inside our layouts we have access to each individual `BlogArticle`. We can use ```current_article``` to get the info for each article to build up our custom template. `title`, `date` and `body` are just methods to extract the attributes from the individual article. We also used `strftime` to format the date like we did previously on the index page.

``` erb

<%= current_article.title %>
<%= current_article.date.strftime('%b %e') %>
<%= current_article.body %>
```

As already mentioned, a simple conditional is in charge of handling data that’s provided optionally for each individual post via its frontmatter—which is delimited by three dashes. 

``` erb

<% if current_page.data.soundcloud_id %>

  ...

<% end %>

```

In case you need a refresher, we get access to the data via the ```current_page``` object and ask its `data` for the value we stored in the frontmatter via its key. In our example, the key we need is ```soundcloud_id```. If our template finds this key via the conditional, it displays the widget and interpolates the SoundCloud id for that track to find the right one. If it’s just a plain blog post, we don’t need to provide the ```soundcloud_id``` in the frontmatter and the SoundCloud widget won’t get embedded. Doesn’t get any more simple than that.

##### Example frontmatter for a podcast post with a SoundCloud widget

``` erb

---                                                                                                                                 
title: My super awesome eleventh long assed titled post that breaks into another line                                               
date: 2015-11-30 22:14 UTC                                                                                                          
soundcloud_id: 138095821                                                                                                            
tags:                                                                                                                               
---                                                                                                                                 
Your awesome podcast episode description …

<%= lorem.sentences 10 %> 

– Question #01

– Question #02

...

```

When you click on “share” on any of the SoundCloud tracks you get the option to embed an `iframe` for that track and just need to copy paste somewhere in your code. We use this iframe as a basis and use the id for the track we need to interpolate it into the url. There are two options, a big and a small widget—I chose the big one. The other has the advantage of being a bit more customizeable—you can adjust the color for the play button for example. Up to you…

``` erb

<section class='soundclould-player-big'>
  <iframe 
    width="100%" 
    height="450" 
    scrolling="no" 
    frameborder="no" 
    src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/<%= current_page.data.soundcloud_id %>&amp;auto_play=false&amp;hide_related=false&amp;show_comments=true&amp;show_user=true&amp;show_reposts=false&amp;visual=true">
  </iframe>
</section>

```

The magic happens at this part.

``` erb

...api.soundcloud.com/tracks/<%= current_page.data.soundcloud_id %>&amp;auto_play=...

```

After we asked if this data is available to us via the conditional, we use the fronmatter data to inject the id to display the track we want. Pretty neat and as straightforward as it gets. Let’s have another look how things turned out so far:

##### Screenshot

{% img /images/middleman/middleman_05_build/detail_page_widget_no_styles.png %}

Makes you wanna throw up huh? Me too. On the bright side, we have all the structure and data we need. And see, because we nested the `blog-layout` inside the `layout` layout, we get the benefit of having the footer already there at the bottom. No need to duplicate things—cool! With just a little bit of styling we might turn things around and make this look a bit more decent. 

##### source/stylesheets/all.sass

``` sass

@import 'posts_detail'

```

##### source/stylesheets/_posts_detail.sass 

``` sass

#main
  +outer-container

article.article-detail
  +shift(2)
  +span-columns(8)

.detail-post-title
  color: $matcha-green
  font-size: 1.7em
  margin-top: 40px

.detail-post-date
  font-size: 1.1em
  color: $text-color

.article-detail p
  font-size: 1.05em
  margin-bottom: 4em
  color: $text-color
  line-height: 1.35em

.soundclould-player-big
  margin-bottom: 50px

```

Let’s have another quick peak.

##### Screenshot

{% img /images/middleman/middleman_05_build/detail_page_widget_with_styles.png %}

Well, it’s getting there. Let’s commit for now, and do some housekeeping after that.

##### Shell

``` bash
git add --all
git commit -m '1st attempt at post detail page w/ podcast option
               Adds blog-layout
               Adjust config for blog-layout
               Adds styles for detail page
               Adds Sass partial
               Imports Sass partial
               Updates blog post’s frontmatter'

```

+ ### Style Duplications

The avid reader might have already spotted what we should clean up next. There is a bit of duplication in `_posts_detail.sass` and `_index_posts.sass`. I’d like to extract the duplicated styles into a separate Sass file called `_blog_post_extractions.sass`. I’m experimenting with this technique lately—an idea that I got from Object Oriented Programming. Things like BEM or SMACSS can be great, especially for bigger projects with bigger teams if they have settled for following conventions, but for smaller projects I’m always looking for frictionless, dead simple solutions. I’ll give this a try until the next new shiny thing convinces me of a better approach.

##### source/stylesheets/all.sass

``` sass

@import 'bourbon'
@import 'base/base'
@import 'neat'

@import 'blog_post_extractions'

@import 'footer'
@import 'index_posts'
@import 'posts_detail'

```

##### source/stylesheets/_blog_post_extractions.sass

``` sass

#main, .posts
  +outer-container

.posts p, .post-title, article.article-detail
  +shift(2)
  +span-columns(8)

.post-title a, .detail-post-title
  color: $matcha-green

.post-title, .detail-post-title
  font-size: 1.7em

.posts p, .article-detail p
  font-size: 1.05em
  line-height: 1.35em

.posts p, .article-detail p, .detail-post-date, .post-date
  color: $text-color

.posts p, .article-detail p
  margin-bottom: 4em

```

If you compare the above with the original files we extracted these styles from below, you can see that we got rid of a nice chunk of duplication. It is also easy to understand and find because I import such extracted files right on top in `all.sass`. It’s easy to spot these extractions for people new to the code base. In this case I use this files to collect exracted styles that apply to blog posts. A similar approach could work duplications across different appearances of sidebars, devices or similar—there should be a common thread though. 

##### source/stylesheets/_index_posts.sass

``` sass

.post-title
  a
    +transition(color .4s ease-in-out)
    &:hover
      color: $text-color

.post-date
  font-size: 0.7em
  margin:
    left: em(-80px)
    right: em(20px)

```

##### source/stylesheets/_posts_detail.sass

``` sass

.detail-post-title
  margin-top: 40px

.detail-post-date
  font-size: 1.1em

.soundclould-player-big
  margin-bottom: 50px

```

The previous files are now a lot smaller, nice and tidy—exactly how I like them. Files are cheap so I don’t care if I have lots of them that all do their specific little job. A separation of concerns kinda thing. It’s not a perfect solution, but it’s so dead simple for small stuff that I like experimenting more with that approach. I’m not sure how much scaleable this is though. For a podcast site it should be O.K. though I feel.

???
Over time, one of my favorite parts of working with any code is sitting down to find duplication and erradicate it as much as possible. Not only in OOP, any form of duplication is enemy number one in killing projects—or in nastyfying them over time at least. I encourage you to learn all kinds of approaches to deal with that and also—even more importantly— to find your own strategies that work best for you and your team. This part of fronend development is a lot less guarded by programming principles, design patterns and so forth and therefore a lot more “Alice in Wonderland” rabbit holes that leave new team members or colleagues tripping out of their minds since a lot of people are cooking their own secret sauce. Anyway…
???

We should also comment out our visual grid in `source/stylesheets/base/_grid-settings.scss` and see how it looks:

##### Screenshot

{% img /images/middleman/middleman_05_build/detail_page_widget_with_styles-no-grid.png %}

Same as before but with much cleaner styles. Me likey! Time to commit and for deploying our changes.

##### Shell

``` bash

git add --all
git commit -m 'Extracts styles into _blog_post_extractions
               Extracts duplications from
                 _index_posts.sass
                 _posts_detail.sass
               Imports styles' 

middleman deploy

```

Let’s go to our GitHub Pages page and check if everything works as expected. Ups! On the first glance it looks fine but if we try to go to the detail page from index we get a `404` error message. GitHub can’t find what we need.

##### Screenshot

{% img /images/middleman/middleman_05_build/404.png %}
 
+ ### Relative Links

If you have looked closely, you might have seen that we are missing some info in our url. Now it says something like `http://your_username.github.io/2015/11/30/my-super-awesome-post.html`. What it should say is something like `http://your_username.github.io/matcha-nerdz/2015/11/30/my-super-awesome-post.html`. The `matcha-nerdz` part was completely missing. Don’t worry, this is an easy fix though. We need to activate relative links in our config file.

##### config.rb

``` ruby

set :relative_links, true  

```

Having absolute links on GitHub Pages will point in the wrong direction. With this little change, your links are namespaced relative to your app’s root. As you can see from this little example, deploying early and often is nice to catch things like that. Finding these things out of context when you are already working on something completely different and you have to dig deep where bugs might come from can mess with your flow tremendously.


##### Shell

``` bash
git commit --all
git commit -m 'Set relative_links in config.rb'

middleman deploy

```

Everything should work fine now. The typography and is not perfect yet but I’d like to move on and fine tune these things once the site is setup the way we need it. Therefore we go back to the index list and adjust it a little bit. Let’s have a look:

##### Screenshot

{% img /images/middleman/middleman_05_build/index-list-long-no-podcast.png %}

+ ### Index List Player

As you can see, were are missing the audio widget and the length of the displayed post is not ideal for an index list. Let’s fix that next. I want to use the smaller SoundCloud player to display the podcast episode in the index list. Therefore it does not make sense to extract a partial for the player for both the index and the detail page—each page needs their own widget. If you like to use only one of the players for both layouts you should definitely extract a partial for it. I’ll leave that steop to you since you already learned how this is done.  

##### source/index.html.erb

``` erb

...

<div class='posts'>
  <% page_articles.each_with_index do |article, i| %>
    <h2 class='post-title'><span class='post-date'><%= article.date.strftime('%b %e') %></span> <%= link_to article.title, article %></h2>

    <% if article.data.soundcloud_id %>
    <section class='soundclould-player-small'>  
      <iframe 
        width="100%"
        height="166" 
        scrolling="no" 
        frameborder="no" 
        src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/<%= article.data.soundcloud_id %>&amp;color=ff5500&amp;auto_play=false&amp;hide_related=false&amp;show_comments=true&amp;show_user=true&amp;show_reposts=false"></iframe>
    <% end %>
    </section>

    <%= article.body %>
  <% end %>
</div>

...

```

The code example is focused on the section where we iterate over ```page_articles```. I added a conditional that only displays the audio widget if the article has a ```sound_cloud_id``` in the frontmatter of the article—which we access via its data attribute. It’s very similar to the way we solved this previously. In this case we used the block parameter `article` to access the info we need.

##### Screenshot

{% img /images/middleman/middleman_05_build/index-list-long-with-small-podcast.png %}

Getting there. Let’s shorten the displayed text as well before we apply a few styles. In the index list we only want to see something like a 300 character summary—not too much but definitely also not too little text. Experiment on your own and see what works best for your needs.

First we need to add the gem `Nokogiri` to our `Gemfile`, bundle it and adjust `source/index.html.erb` a bit.

##### Gemfile

``` ruby

gem 'nokogiri'

```

##### Shell

``` bash

bundle install

```

In index we need to change only one line. I left a comment for what needs to be replaced. We use the summary method and supply it with the number of characters we want to see per article in the index list.

##### source/index.html.erb

``` erb
<%# article.body %>
<%= article.summary(300) %>

```

##### source/stylesheets/_index_posts.sass

And we should add the styles for the small SC player on `.soundcloud-player-small` to our extracted file.

##### source/stylesheets/_blog_post_extractions.sass

``` sass

.posts p, .post-title, article.article-detail, .soundclould-player-small
  +shift(2)
  +span-columns(8)

```

Nudge the spacing a bit and we’re dont.

``` sass

.soundclould-player-small
  margin-bottom: 1em

```

##### Screenshot

{% img /images/middleman/middleman_05_build/index-list-long-with-small-podcast-summary.png %}

Alright! If you have a bit better dummy text, this should look quite decent by now. Let’s commit!

##### Shell

``` bash

git add --all
git commit -m 'Adds Article Summary & Small Widget to Index
               Adds styles for index list SC widget
               Adds Nokogiri
               Adds optional SC widget to index
               Adds 300 character summary'

middleman deploy

``` 

+ ### Break

I think you earned yourself a cool beverage at this point and we can leave it at that for now. In the next and final article we’ll tweak it a bit further and also add a little something for navigating the site.

+ ### Why SoundCloud? (Optional)

“Why host the podcast on SoundCloud?”, you might ask. Well, my reasoning was simple: First of all, it will quite certainly be around for a long time—something that you can’t necessarily expect from a lot of projects that offer to host your podcast audio files. I don’t wanna get myself in the situation of dealing with migrating tons of already published audio tracks to another service just because someone got acquihired or went bust. Second, it’s dead cheap to host a ton of tracks and they even have a free option for folks who publish tracks only occasionally. The player and its options are alright too—hadn’t any reason to complain about speed or anything so far. The stats are useful as well and there are already people on the platform who are into podcasts and stuff—which is good for the discovery factor. Don’t get me wrong, there are plenty of reasons why I wanted to hug somebody gently around the neck when dealing with uploading and silly UX things, but compared to the downsides of bigger headaches with other hosting options, SoundCloud seemed like the most reasonable choice overall. Lastly, I don’t like the custom sites these podcast sites offer. They look super generic and I like to build my own stuff that fits my needs and let’s me also create my own visual identity. This is just my own personal opinion and I’m not affiliated to SC in any way—atm at least. Don’t plan on changing that also.

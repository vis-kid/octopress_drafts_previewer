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

##### Optional Info About Choosing SoundCloud

“Why host the podcast on SoundCloud?”, you might ask. Well, my reasoning was simple: First of all, it will quite certainly be around for a long time—something that you can’t necessarily expect from a lot of projects that offer to host your podcast audio files. I don’t wanna get myself in the situation of dealing with migrating tons of already published audio tracks to another service just because someone got acquihired or went bust. Second, it’s dead cheap to host a ton of tracks and they even have a free option for folks who publish tracks only occasionally. The player and its options are alright too—hadn’t any reason to complain about speed or anything so far. The stats are useful as well and there are already people on the platform who are into podcasts and stuff—which is good for the discovery factor. Don’t get me wrong, there are plenty of reasons why I wanted to hug somebody gently around the neck when dealing with uploading and silly UX things, but compared to the downsides of bigger headaches with other hosting options, SoundCloud seemed like the most reasonable choice overall. Lastly, I don’t like the custom sites these podcast sites offer. They look super generic and I like to build my own stuff that fits my needs and let’s me also create my own visual identity. This is just my own personal opinion and I’m not affiliated to SC in any way—atm at least. Don’t plan on changing that also.

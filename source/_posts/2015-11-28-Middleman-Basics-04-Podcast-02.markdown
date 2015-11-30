---
layout: post
title: Middleman Basics 03-Podcast Site (Part 02)
date: 2015-11-28 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Neat Grid

+ ### Neat Grid 

Where were we? Right now our site doesn’t look to sexy.

**Screenshot**

{% img /images/middleman/middleman_04_build/before_neat_outer-container.png %}

Currently our posts are not aligned to anything and we’re in need of a grid to fix this mess. Our beloved Bourbon Neat to the rescue!. First we’ll add a class **.posts** as a wrapper for our posts and mark it as an **outer-container** that centers the content on the page.

**source/index.html.erb**

``` erb

<div class='posts'>
  <% page_articles.each_with_index do |article, i| %>
    <h2><%= link_to article.title, article %> <span><%= article.date.strftime('%b %e') %></span></h2>
    <!-- use article.summary(250) if you have Nokogiri available to show just
         the first 250 characters -->
    <%= article.body %>
  <% end %>
</div>

```

Then we need to create a new Sass partial for our index styles and apply some magic.

**source/stylesheets/all.sass**

``` sass

@import 'index_posts'

```

**source/stylesheets/_index_posts.sass**

``` sass

.posts
  +outer-container
  background-color: green

```

I also added a background color to make our outer container easily visible—for now.

**Screenshot**

{% img /images/middleman/middleman_04_build/after_neat_outer-container.png %}

**Git**

``` bash

git add -all
git commit -m 'Adds Sass partial for index posts
               Centers content'

```

Recent articles, tags, and calendar stuff is in **layout.erb** and doesn’t concern us atm. We’ll leave it as is for now. Let’s focus instead of making this index list of posts pop. Let’s give the **h2** title a class **post-title** and let title and paragraphs span for 8 (out of twelve) columns across the page. We needed to shift the posts for two columns as well. We want to avoid having our copy running across the whole page and therby exceeding a healthy reading width of 65-85 characters.

**source/stylesheets/_index_posts.sass**

``` erb

<div class='posts'>
  <% page_articles.each_with_index do |article, i| %>
    <h2 class='post-title'><%= link_to article.title, article %> <span><%= article.date.strftime('%b %e') %></span></h2>
    <!-- use article.summary(250) if you have Nokogiri available to show just
         the first 250 characters -->
    <%= article.body %>
  <% end %>
</div>

```

``` sass

.posts p, .post-title
  +shift(2)
  +span-columns(8)
  background-color: yellow

```

**Screenshot**

{% img /images/middleman/middleman_04_build/after_shift_span-columns.png %}

Now we’re talking. Our content is aligned and nicely centered on the page. What we’re missing though is any form of visual hierarchy. Our **h2** titles are not much bigger than the content of our posts. To provide a better reading experience, we want to make sure titles and their corresponding text have better visual contrast than that.

We need better text to work with first. Let’s make first use of a Middleman helper for dummy text. Let’s add an **erb** extension to our blogposts and add the following to our test posts. Btw, we need the **.erb** extension only because we want to run som ruby code between this construct `<%= %>`.

**source/posts/2012-01-01-example-article.html.markdown.erb**

``` erb

This is an example article. You probably want to delete it and write your own articles!
<%= lorem.sentences 5 %>

```

I’ll show you in a minute what what got here, but first a few more styles as well.

**source/stylehseets/_index_posts.sass**

``` sass

.post-title
  font-size: 1.7em

.posts p
  font-size: 1.05em
  margin-bottom: 4em

```

**Screenshot**

{% img /images/middleman/middleman_04_build/after_title_p_font-sized.png %}

A bit easier on the eyes isn’t it? We have adjusted the font sizes to a reasonable size and put a little margin in between posts. In terms of hierachy, its a good start.


**Git**

``` bash

git add --all
git commit -m 'Adjusts size for title and body text
               Adds dummy text
               Adds .erb extension to dummy posts'

```

I think we should take care of these helpless floating elements on the bottom first. Let’s pack “Recent Articles” and “Tags” in a footer and get rid of “By Year”. This markup is part of the global layout in **source/layouts/layout.erb**. Find the code in the **aside** tag below **yield** and adapt it like this:

**source/layouts/layout.erb**

``` erb

<footer>

  <div class='recent-posts'>
    <h3>Recent Posts</h3>
    <ol>
      <% blog.articles[0...10].each do |article| %>
        <li><%= link_to article.title, article %> <span><%= article.date.strftime('%b %e') %></span></li>
      <% end %>
    </ol>
  </div>

  <div class='footer-tags'>
    <h3>Tags</h3>
    <ol>
      <% blog.tags.each do |tag, articles| %>
        <li><%= link_to "#{tag} (#{articles.size})", tag_path(tag) %></li>
      <% end %>
    </ol>
  </div>

</footer>

```

The above default of looping through our posts and tags that comes with Middleman is fine. I want to have it a bit smarter though and introduce shuffling to both recent posts and tags. That way, the user doesn’t only see that last ten articles or a huge list of tags but a randomized version of both that is always ten items long—which does not consume a whole lot of space and let’s me align both items in the footer more beautifully.

**source/layouts/layout.erb**

``` erb

<footer>

  <div class='recent-posts'>
    <h3>Recent Posts</h3>
    <ol>
      <% blog.articles.shuffle[0...10].each do |article| %>
        <li><%= link_to article.title, article %> <span><%= article.date.strftime('%b %e') %></span></li>
      <% end %>
    </ol>
  </div>

  <div class='footer-tags'>
    <h3>Tags</h3>
    <ol>
      <% blog.tags.to_a.shuffle[0...10].each do |tag, articles| %>
        <li><%= link_to "#{tag} (#{articles.size})", tag_path(tag) %></li>
      <% end %>
    </ol>
  </div>

</footer>

```

I think we improved the user experience quite a bit through that little enhancement. Ruby made our job super easy.



No this markup only need a little push for better alignment. We create a new Sass partial for just the footer.

**source/stylesheets/_footer.sass**

``` sass

footer
  +outer-container
  border-top: 1px solid $base-border-color
  padding-top: 4em
  padding-bottom: 4em

.recent-posts, .footer-tags
  h3
    font-size: 1.7em
  li
    font-size: 1.05em

.recent-posts
  +span-columns(6)
  +shift(2)
  background-color: tomato

.footer-tags
  +span-columns(2)
  background-color: cyan

```

**source/stylesheets/all.sass**

``` sass

@import 'footer'

```

In order to have some tangible test data, I added a couple more example posts via the middleman generator and gave it some dummy lorem text.

**Shell**

``` bash

middleman article 'Your fancy title'

```
I should probably mention that I needed to add an **.erb** extension to these new posts —needs ruby. In order to make the lorem dummy text work, I also added a couple of dummy tags in the frontmatter of the example posts. 

**source/posts/2012-01-01-example-article.html.markdown.erb"**

``` html

---
title: Example Post
date: 2012-01-01
tags: example, bourbon, neat, middleman
---

This is an example article. You probably want to delete it and write your own articles!
<%= lorem.sentences 5 %>

```

The goal was to have at least ten posts and tags to see if everything aligns properly. Let’s see what we got here:

**Screenshot with dummy bg**

{% img /images/middleman/middleman_04_build/footer-shuffled-10-items-span-columns.png %}

Ok, now the background colors have fullfilled their duty for now. Let’s kill them for a moment and check if we’re happy with the actual result:

**Screenshot without dummy bg**

{% img /images/middleman/middleman_04_build/footer-shuffled-10-items-span-columns-without-bg.png %}

I think it looks decent and we can leave like that for now. Time to commit our changes.

**Git**

``` bash
git add  ../layouts/layout.erb
gco -m 'Adapts layout and adds footer'

git add ../posts/*.markdown.erb
git commit -m 'Adds a bunch of dummy posts with:
              dummy lorem text
              dummy tags'

git add ../stylesheets/_footer.sass ../stylesheets/all.sass
git commit -m 'Adds styles for footer and imports Sass partial'

```

Before we move on we should deploy to GitHub Pages, check our progress and make sure we’re not running into any surprises.

**Shell**

``` bash

middleman deploy

```

Open your browser and go to `yourusername.github.io/your_project_name` and see if you’re happy with your site so far.

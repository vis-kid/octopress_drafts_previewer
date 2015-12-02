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

+ Posts Index
+ Footer
+ Color

+ ### Posts Index

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

**source/index.html.erb**

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

**source/stylesheets/_index_posts.sass**

``` sass

.posts p, .post-title
  +shift(2)
  +span-columns(8)

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

**source/stylesheets/_index_posts.sass**

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

+ ### Footer

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

The above default of looping through our posts and tags that comes with Middleman is fine. I want to have it a bit smarter though and introduce shuffling to both recent posts and tags. That way, the user doesn’t only see the last ten articles or a huge list of tags but a randomized version of both that is always ten items long—which does not consume a whole lot of space and let’s me align both items in the footer more beautifully. I renamed the **h3** for posts as well.

**source/layouts/layout.erb**

``` erb

<footer>

  <div class='recent-posts'>
    <h3>Random Posts</h3>
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

.footer-tags
  +span-columns(2)

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

What should we do next? You’re right, the footer screams in bigh letters EXTRACTION! We’re gonna take the **footer**, create a new folder for partials and put the markup in there. In turn, we need to render that partial from **layout.erb**.

**source/layouts/layout.erb**

``` erb

<body>
  
  <div id="main" role="main">
    <%= yield %>
  </div>

  <%= partial "partials/footer" %>
  
</body>

```

**source/partials/_footer.erb**

``` erb

<footer>

  <div class='recent-posts'>
    <h3>Random Posts</h3>
    <ol>
      <% blog.articles.shuffle[0...10].each do |article| %>
        <li><%= link_to article.title, article %></li>
      <% end %>
    </ol>
  </div>


  <div class='footer-tags'>
    <h3>Tags</h3>
    <ol>
      <% blog.tags.to_a.shuffle[0...10].each do |tag, articles| %>
        <li><%= link_to "#{tag}", tag_path(tag) %></li>
      <% end %>
    </ol>
  </div>

</footer>

```

I suppose you paid close attention and saw that I got rid of the date for the list of articles in the footer. I did this for two reasons. First of all we’re gonna save a bit more space so that we don’t easily run in the scenario that the alignment with the tags breaks when the title for the post is a bit longer. Secondly, I thought it is a bit distracting and doesn’t add too much. This list of randomzied articles in the footer are a sneaky way for the audience to discover new stuff. The date doesn’t play a big role in that. The same goes for the number of articles for the tag links. They waste space without adding too much value. Also, if you don’t have too many articles for a certain tag, it doesn’t look empty right away. I’d rather have more space for a stable layout. It also feels a bit more clean, but that is clearly very subjective.

**Screenshot**

{% img /images/middleman/middleman_04_build/footer-posts-without-date-tagnumbers.png %}

**Git**

``` bash

git add --all
git commit -m 'Extracts footer into partial 
               Removes date from posts in footer'

               Removes number of articles for tags in footer
               Didn’t provide enough value to sacrifice space 

```

While we’re at it. Let’s take care of the date in the titles. I think in size it’s way too prominent which does not improve our visual hierarchy and I don’t like having it at the end of the title. I’d rather stick it on the other side and use it as a visual anchor that doesn’t jump around with varying title lengths.

**source/index.html.erb**

``` erb

<div class='posts'>
  <% page_articles.each_with_index do |article, i| %>
    <h2 class='post-title'><span class='post-date'><%= article.date.strftime('%b %e') %></span> <%= link_to article.title, article %></h2>
    <%= article.body %>
  <% end %>
</div>

```

**source/stylesheets/_index_posts.sass**

``` sass

.post-date
  font-size: 0.7em
  margin:
    left: em(-80px)
    right: em(20px)

```

The title of the post is reordered and starts with the span hat contains the date. I left a little whitespace between the span and the link to the article because if I align the date with the article body text for smaller screens then I have a natural one character space between the date and the title—and don’t need to use Sass unneccessarily.

The Sass code is straightforward. The negative margin helps me to visually anchor the date to the left of the title and I used a Bourbon function to convert the pixels into **em*s. Simple but I like the hierarchy we achieved. The eyes have something to jump to via the dates and the rest has enough whitespace so that we can stay away from using borders to divide our posts. Me, happy!

**Git**

``` bash

git add ../index.html.erb ../stylesheets/_index_posts.sass
git commit -m 'Changes order for date and post title on index page
               Styles date to create visual anchor'

```

**Shell**

``` bash

middleman deploy

```

**Screenshot**

{% img /images/middleman/middleman_04_build/post-title-smaller-anchors.png %}

+ ### Color

Let’s bring this thing to life a bit—but not too much. Less is more! I went to [COLOURlovers](http://www.colourlovers.com/) and played with a couple of color palettes. Sites with various palletes can help to give you ideas where you wanna go. Watch out for solutions that can enhance your visual hierarchy but stay away from colors that are screamishly loud. I realize that this is vague since colors can be very subjective and culturally loaded—that’s how I approach it atm anyway. 

Overall, it’s not an easy topic and I wish people would pay more attention to it. You certainly shouldn’t just poke around on a color wheel when you lack the experience. If you’re new to this, educate yourself and don’t just pick a color because you feel like it or because it looks cool or something. A reasonable use of color can be very powerful—on the other hand it can look very comical very quickly as well.

Back to business, after playing with some ideas, I added two new global colors to my Sass variables file from Bitters. **$matcha-green** is now the color I wanna use for my identity and can reuse this variable wherever I please. Should I change my mind about what green I want, I will need to change it only in once place! Also, I wasn’t too happy with the default color for body copy. Using a Sass function I darkened one of the preset colors from Bitters by 20 percent and stored that as **$text-color**.

**source/stylesheets/base/_variables.scss**

``` scss

$matcha-green: #78B69D;
$text-color: darken($medium-gray, 20%);

```

Post titles on hover, as well as dates and body copy got the new text color. The default was too dark imho.

I also added a slight transition through a Bourbon mixin for hovering over **.post-title**. This changes from **$matcha-green** to **$text-color** over **.4** seconds. Check my articles about Bourbon Mixins if you wanna know more. In case you wonder about the **ease-in-out** part, it’s one of 32 ways to time transitional behaviour. ($ease-in-out, as a **$**variable, like in the documentation will through an error) It’s a small enhancement but looks a lot better than browser defaults. To make this work I also had to uncomment the default transition behaviour from Bitters first. 

**source/stylesheets/base_typography.scss**

``` scss

// transition: color $base-duration $base-timing;

```
**source/stylesheets/_index_posts.sass**

``` sass

.post-title
  font-size: 1.7em
  a
    +transition(color .4s ease-in-out)
    color: $matcha-green
    &:hover
      color: $text-color

.post-date
  color: $text-color

.posts p
  color: $text-color

```

Lastly, I adapted the colors for the footer as well. This gives us a coherent appearance and hopefully a bit understatement. The transitional behavior needed to speed up for the links in the footer. Since they are grouped so tight together I felt it wold be better if they are a bit snappier and underlined as well. In terms of color, I did the oposite as with the titles in the index list. Since the footer list doesn’t need to be as present on the page—especially with so little distance between them—I gave them the default gray text color and only use the **$matcha-green** when hovering. In this example we “only” use whitespace and the size of the type to achieve hierarchy. Oh, and the border above the footer needed a bit opacity via the Sass **rgba** function. I figured that 30 percent opacity is just enough to do its job without sticking out too much.

**source/stylesheets/_footer.sass**

``` sass

footer
  border-top: 1px solid rgba($text-color, .3)

.recent-posts, .footer-tags
  color: darken($medium-gray, 20%)
  a
    +transition(all .1s ease-in-out)
    color: $text-color
    &:hover
      color: $matcha-green
      border-bottom: 2px solid $matcha-green

```

Not too shabby for such a small amount of code. Exactly how I like it—the less “code” you write the less bugs you bite!

**Screenshot**

{% img /images/middleman/middleman_04_build/after-color-changes-index.png %}

**Git**

``` bash
git add --all

git commit -m 'First attempt at tuning colors
               Adds new brand color as $matcha-green
               Adds new $text-color:
                 Body copy
                 Post titles hover
                 Footer headers
               Takes care of hover transitions
                 Comments out Bitters default transition'

```

One little thing that bugs me now is the line height of the body copy. Let’s tweak that a little too.

**source/stylesheets/_index_posts.sass**

``` sass

.posts p
  line-height: 1.35em

```

**Screenshot**

{% img /images/middleman/middleman_04_build/index-body-copy-line-height.png %}

**Git**

``` bash
git add ../source/stylesheets/_index_posts.sass
git commit -m 'Adjusts line-height for body copy on index'
```

**Shell**

``` bash

middleman deploy

``` 

Todo
change screenshot for tags with removed numbers. After commiting newest changes checkout the relevant commit

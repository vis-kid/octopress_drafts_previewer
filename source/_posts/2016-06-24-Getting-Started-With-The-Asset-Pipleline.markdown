---
layout: post
title: Getting Started With The Asset Pipleline
date: 2016-06-24 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, Ruby, Ruby on Rails, Asset Pipeline, Sass, CSS, JS, JavaScript]
---

{% img /images/Asset-Pipeline/Siphon_Lock_Overview.png %}

## Topics

+ Asset Pipeline?
+ Concatenation & Compression?
+ Higher-Level Languages
+ MD5 Fingerprinting?
+ Organization
+ Using The Pipeline

## Asset Pipeline?

What does the Asset Pipeline have to offer? Despite the fancy name, it’s relatively easy to break down what it can do for you. The main thing it takes care for you is concatenation, minification and preprocessing of assets that are written in higher-level languages like CoffeeScript or Sass for example. That can bring not only a boost in quality and speed but also in convenience. In this regard we could talk about convenience over configuration.

The other thing that should not be underestimated is organization. The pipleline offers a solid frame to place your assets. On smaller projects this might not seem that important, sure, but bigger projects might not recover easily from going in the wrong direction on subjects like this. Sure it’s not the best example in the world, but just imagine a project like Facebook or Twitter having shitty organized assets for their CSS and JS. Not that hard to imagine that this would breed trouble, big time, and that peeps who have to work and build on such a basis have not an easy time to love their jobs.

As with many things in Rails, there is a conventional approach of how to handle assets. This makes it easier to onboard new people and to avoid having developers and designers being too obsessed with bringing their own dough to the party. When you join a neww team, you want to be able to get up to speed quickly and hit the the ground running. Needing to figure out the special sauce of how other peeps organized their assets is not exactly helpful with that. In extreme cases this can even be regarded wasteful or even burning money unnecessarily—but let’s not get too dramatic here.

The Asset Pipeline is not exactly news to people in the business but for beginners it can be a little tricky to figure it out right away. Developers don’t exactly spend a ton of time on front-end stuff, especially when they start out they are busy with lots of moving parts that have nothing to do with HTML, CSS or the often bashed JS bits—no offense JS, I got no beef with you these days.

So this article is for the beginners among you and I recommend taking a look at the pipeline right away and get a good grip on this topic right away—even if you won’t expect to work much on markup or front-endy things much in the future. It’s kind of an essential aspect in Rails and not that huge of a deal to figure out quickly. Plus your team members, especially designers who spend half their lives in these directories will put less funny stuff in your coffee if you have some common ground where you meet half way.

## Concatenation & Compression?

Why do we need that stuff? Long story short, you want your apps to load faster. Period! Anything that helps with that is welcome. Well, of course you can get away without optmizing for that but it has too many advantages that one simply can’t ignore. Compressing file sizes and concatenating multiple asset files into one “master” file that is downloaded by a client like a browser is also not that much work. It is mostly handled by the pipleline anyway.

Reducing the number of requests for rendering pages is very efffective at speeding up the rendering of pages. Instead of having maybe 10, 20, 50 or whatever number of requests before the browser is finished getting your assets you could potentially only have one for each CSS and JS assets. That is not only nice, this was a game changer at some point. These kinds of improvements in web development should not be neglected because we are dealing with miliseconds as a unit. Lots of requests can add up pretty substantially. And after all, the user experience is what matters most for successful projects. Having users who need to wait just a little bit extra before they can see content on your page can be a massive dealbreaker.

Minification is cool because it gets rid of unnecessary stuff in your files. Whitespace and comments basically. These compressed files are not made with human readability in mind but for machine consumption. The files will end up looking a bit cryptic and hard to read but its still all valid CSS and JS code—just without any excess whitespace to make it readable and understandable for humans. The JS compression is a bit more involved though. Web browsers don’t need that formating and that let’s us optimize these assets for downloading and rendering web pages and their behaviour.

## Higher-Level Languages

Sounds more fancy than it is. We are talking about Sass, CoffeeScript, Haml, Slim, Jade and things like that. Through them we can write in languages that can be more convenient and efficient to work in. They need to be precompiled which produces takes assets and transforms them into CSS, HTML and JS before they are deployed and rendered by the browser.

You might have heard about them already but as a beginner might not be up to speed why you would need to learn yet another language—especially if you are still busy learning to write classical HTML and CSS. These languages were created to deal with shortcomings that developers wanted to iron out and not deal with on a daily basis. 

### Sass

Sass stands for ”Syntactically Awesome Style Sheets” and is much more potent than pure CSS. It let’s us write smarter stylesheets that have a few programming tools baked in. What are we talking about here exactly? You can declare variables, nest rules, write functions, create mixins, easily import partials and lots of other stuff that programmers wanted to have available playing with styles. It’s a pretty rich tool by now and is also excellent for organzing stylesheets—for big projects I would even call it essential. These days, I’m not sure if I wouldn’t stay away from a large app that isn’t using a tool like Sass—whatever non-Ruby centric frameworks have to offer.

For your current concerns, there are two syntax versions of Sass you need to know about. The Sassy CSS version, aka SCSS, is the more widely adopted one. It stays closer to plain CSS but offers all the fancy extensions developers and designers came to love playing with stylesheets. It uses the `.scss` file extension and looks like this:

###### SCSS

``` Sass

#main p {
  color: #00ff00;
  width: 97%;

  .redbox {
    background-color: #ff0000;
    color: #000000;
  }
}

```  

Visually, it’s not that different than CSS—that’s why it is the more popular choice, especially among designers I think. One of the really cool and handy aspects of SCSS, and Sass in general is the nesting aspect. The indented syntax, the Sass syntax that has the `.sass` file extension, let’s you avoid to deal with all these silly curly braces and semicolons that lazy devs like to avoid. How does it do that? Without the brackets, Sass uses indentation, white space essentially, to separate its properties. It’s pretty rad and looks really cool too. To see the differences, I’ll show you both versions.

###### SCSS

``` Sass

#main {
  color: blue;
  font-size: 0.3em;

  a {
    font: {
      weight: bold;
      family: serif;
    }
    &:hover {
      background-color: #eee;
    }
  }
}

```

###### Sass

``` Sass

#main
  color: blue
  font-size: 0.3em

  a
    font:
      weight: bold
      family: serif
    &:hover
      background-color: #eee

``` 

Pretty nice and compact huh? But you can see that both versions need processing before it turns into valid CSS that browsers can understand. If you would hit browers with something like Sass, they would have no idea what’s going on.

I prefer it the indendet syntax but to be fair, whitespace sensitive Sass syntax is less popular than the SCSS syntax which is closer to CSS. Both have the same programming tricks up their sleeves though and are excellent in making the process of writing your styles much more pleasant and effective. Lucky for us that Rails and the Asset Pipeline makes it so easy to work with either of these—pretty much out of the box.

### Slim & Haml

Similar arguements can be made about the benefits of something like Slim and Haml for writing more compact HTML code. Both let you write markup much more reduced—no closing tags and cool abbreviations for tagnames and such. This results in shorter bursts of markup that is meant to be easier to skim and read. Me personally, I’m not a huge fan of Haml, never was, but Slim is not only fancy and convenient, it is also very smart. For peeps who like the indented Sass syntax it’s something I would definitely recommend to try out. Let’s have a quick look:

###### Slim

``` Haml

#content
  .left.column
    h2 Welcome to our site!
    p= print_information
  .right.column
    = render :partial => "sidebar"

```

###### Haml

``` Haml

#content
  .left.column
    %h2 Welcome to our site!
    %p= print_information
  .right.column
    = render :partial => "sidebar"

```

And both result in the following ERB-enhanced HTML in Rails:

``` html

<div id="content">
  <div class="left column">
    <h2>Welcome to our site!</h2>
    <p>
      <%= print_information %>
    </p>
  </div>
  <div class="right column">
    <%= render :partial => "sidebar" %>
  </div>
</div>


```

The difference on the surface are not that big but I never quite got why Haml wants me to write all these extra `%` to prepend tags. Slim found a solution that got rid of these and therefore I salute you! The other differences are under the hood and are not within the scope of this piece today. Just wanted to whet your appetite.

As you can see, both preprocessors reduce the amount you need to write significantly. Yes, you need to learn another language and it reads a bit weird at first but at the same timeI feel the removal of so much clutter is totally worth it. Also, once you know how to write ERB-flavored HTML, you will be able to pick up any of these preprocessors rather quickly. No rocket science—same goes for Sass btw.

There are two handy gems for using either of them in Rails. Do yourself a favor and check them out when you are tired of writing all these tiresome opening / closing tags.

``` ruby

gem "slim-rails"

gem "haml-rails"

```
### CoffeeScript

The short arguemnt for working with CoffeeScript was that it helped to overcome some shortcomings that JS was carrying around. Especially for people coming from Ruby who preferred to write JS in a Ruby-like manner, CoffeeScript was quite popular for a while. Not sure how adoption rates are these days, it seems to have slowed down a bit but it is still supported by default by Rails.

?? Example



### MD5 Fingerprinting?

Rails creates a fingerprint for each filename.

``` bash

navbar-908e25f4bf641868d8683022a5b62f54.css

```

That way it can easily determine if a file has changed its contents and can update the file. If nothing has changed it is cached by the web browser for future requests.

When you change the contents, a new fingerprint will created and added to the filename of your asset.

So the filename changes with changes to the contents of the file. That way Rails can easily compare versions of the same file and decide if it needs let the browser request a new version for it.

The added hash is a mathematical function that converts the contents of a file into a unique sequence of 32 hexadecimal digits

That means that you get the same hashed result when you apply the function to the same content twice—or how often you like.

``` bash

MD5("The quick brown fox jumps over the lazy dog")

```

``` bash

9e107d9d372bb6826bd81d3542a419d6

```

The process of fingerprinting is very effective at ensuring that files are updated only when necessary.

You can disable it in config.assets.digest

## Organization

You have three options to place your assets:

+ `app/assets`
+ `lib/assets`
+ `vendor/assets`

These divisions are more of the logical kind and don’t represent any technical restrictions or limitations. You can place assets in any of them and see the same results. But for smarter organization, you should have a good reason to place content at their proper place.

The `app/assets` folder is for assets that are specifically for this particular app. Images, JS and CSS files that are sort of tailor made for a particular project. The `lib/assets` folder on the other hand is the home for your own code that you can reuse from project to project—things that you yourself might have extracted and can take from project to project—assets that you can share between applications. `vendor/assets` is another step more outward. It’s the home for assets that you reused from other, external source. Plugins and frameworks from third parties.

These are the three locations that Sprockets will look for assets. Within the above directories, you are expected to place your assets within `/images`, `/stylesheets` and `/javascripts` folders. But this is more of a conventional thing, any assets withing the ```*/assets``` folders will be traversed. You can also add subdirectories as needed, Rails won’t complain and precompilethese files anyway.

Search path inspection via `rails console`:

```bash

Rails.application.config.assets.paths

```

What you will get returned is an array with all the assets that are available and found by Sprockets.

``` bash

=> ["/Users/username/projects/rspec-test-app/rspec-dummy/app/assets/images", "/Users/username/projects/rspec-test-app/rspec-dummy/app/assets/javascripts", "/Users/username/projects/rspec-test-app/rspec-dummy/app/assets/stylesheets", "/Users/username/projects/rspec-test-app/rspec-dummy/vendor/assets/javascripts", "/Users/username/projects/rspec-test-app/rspec-dummy/vendor/assets/stylesheets", "/usr/local/lib/ruby/gems/2.3.0/gems/turbolinks-2.5.3/lib/assets/javascripts", "/usr/local/lib/ruby/gems/2.3.0/gems/jquery-rails-4.1.1/vendor/assets/javascripts", "/usr/local/lib/ruby/gems/2.3.0/gems/coffee-rails-4.1.1/lib/assets/javascripts"]

```

The nice thing about all of this is easy to miss. It’s the aspect of having conventions. That means that developers—and designers as well actually—can all have certain expectations wehre to look for files. When someone new joins a project, they have a good idea how to navigate a project and where to put new work. That is not only convenient, but it can also be a huge time saver and prevents not so good ideas from reinventing the wheel all the time. Yes, convention over configuration can sound a bit boring at times, not that exciting in a way, but it is powerful stuff nontheless. It keeps us focused on what matters, the work itself and good team collaboration.

The assets paths mentioned so far aren’t set in stone though. You can add custom paths as well. You need to go to `config/application.rb` and add custom paths that you want to be recognized by Rails. Let’s take a look how we would add a ```custom_folder```.

###### config.application.rb

``` ruby

module RspecDummy
  class Application < Rails::Application
    config.assets.paths << Rails.root.join("custom_folder")
  end
end

```

When you now check the search path again, you will find your custom folder being part of the search path for the asset pipeline. Btw, it will now be the last object placed in the array:

###### Terminal

``` bash

Rails.application.config.assets.paths

```

###### Output

``` bash

["/Users/vis_kid/projects/rspec-test-app/rspec-dummy/app/assets/images", "/Users/vis_kid/projects/rspec-test-app/rspec-dummy/app/assets/javascripts", "/Users/vis_kid/projects/rspec-test-app/rspec-dummy/app/assets/stylesheets", "/Users/vis_kid/projects/rspec-test-app/rspec-dummy/vendor/assets/javascripts", "/Users/vis_kid/projects/rspec-test-app/rspec-dummy/vendor/assets/stylesheets", "/usr/local/lib/ruby/gems/2.3.0/gems/turbolinks-2.5.3/lib/assets/javascripts", "/usr/local/lib/ruby/gems/2.3.0/gems/jquery-rails-4.1.1/vendor/assets/javascripts", "/usr/local/lib/ruby/gems/2.3.0/gems/coffee-rails-4.1.1/lib/assets/javascripts", #<Pathname:/Users/vis_kid/projects/rspec-test-app/rspec-dummy/custom_folder>]

```


## Using The Pipeline

The Asset Piple line was extracted since Rails 4 and is not a core functionality anymore. Sprockets is now in charge of it. It is enabled by default though.

Enabled by default though

Skip pipeline when you initiate project

Rails 4 makes a couple of decisions for you though. It adds gems for Sass, CoffeeScript and compression

Setting asset compression methods


Your CSS, JS, CoffeeScript, Sass, Slim, etc files are now organized in one central and convenient place, under `app/assets`.

The Sprockts middleware??? is reponsible for serving files in this directory—Sprockets is in charge. `app/assets` is the place to put files that still need preprocessing, like turning Sass files into their equivalent CSS files.

Also, files placed in `app/assets` get precompiled automatically for going into production—before being deployed onto a server.That means that the files in your `assets` folder are never served as-is for production—they are expected to be processed first.

?? require_tree

### image_tag

Images placed in `public/assets/images` directory you can use a convenience method to access images without fiddling around with path names yourself.

###### some.html.erb file

``` ruby

<%= image_tag "some-image.png" %>

```

If activated, Sprockets will serve such files if found. When a file like `some-image.png` is fingerprinted like `some-image-9e107d9d372bb6826bd81d3542a419d6.png it will be treated the same way.

If you need other directories within `public/assets/images` or within `app/assets/images` to organize your images, something extra for icons or svg files maybe Rails will have no problem finding them but you need to add the directory’s name first:

``` ruby

<%= image_tag "svgs/some.svg" %>

<%= image_tag "icons/some-icon.png" %>

```

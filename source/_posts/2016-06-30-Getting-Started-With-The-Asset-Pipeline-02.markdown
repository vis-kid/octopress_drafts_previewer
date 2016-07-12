---
layout: post
title: Getting Started With The Asset Pipleline 02
date: 2016-06-30 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, Ruby, Ruby on Rails, Asset Pipeline, Sass, CSS, JS, JavaScript]
---

{% img /images/Asset-Pipeline/Siphon_Lock_Overview.png %}

## Topics

+ Sprockets?
+ Precompiling Assets
+ Asset Links
+ Pimped Styles
+ CSS Compression
+ JS Compression

## Sprockets?

The asset pipeline in Rails is handled by the `sprockets-rails` gem. By default, it is enabled when you create a new Rails application. What are we dealing with here? Sprockets is a Ruby library and helps you manage your JavaScript and CSS assets. On top of that, it takes care compiling as well as pre-processing higher-level languages like Sass, SCSS and CoffeeScript. Preprocessing simply means that the files you write for your styles, HTML or JavaScript get generated after you wrote their code in another language. Since browsers can’t comprehend stuff like CoffeeScript we have to pre-process it first.

There are three gems that play a vital role in all of this:

+ `coffee-rails`
+ `uglifier`
+ `sass-rails`

`sass-rails` let’s you write your Sass-flavor of choice for creating your CSS, `coffee-rails` gives you a nicer syntax for writing you JavaScript and `uglifier` in turn compresses these assets. Btw, `sass-rails` is in charge for compressing CSS files itself. All three gems will be added to your Gemfile automatically.

If you have reason to not use Sprockets with Rails you can skip it from the command line when you initiate your app:

###### Terminal

``` ruby

rails new appname --skip-sprockets

```

This command prevents adding the above mentioned gems to your Gemfile and give you a different version of the `config/application.rb` file.

## Precompiling Assets

For production, your assets need to be precompiled. What are we talking about here? Let’s say you have worked one some new app that works on your local server. Your Sass files and your CoffeeScript flavored JavaScript works magically out of the box. Cool, but what happens if you wanna push this to a production server? Such a server, responsible for delivering this content to a possibly much larger audience, has a few different demands than your local server.

As a default, Rails will look for files that are named `application` and try to precompile them for you.
In this step, assets are compiled from high-level languages like Sass into CSS, they are concatenated together—from multiple files into fewer bundles of files and are cached so that you only load new assets when they have been changed on your end.

### Local & Live Compilation

You have two options where you  want to compile your assets. Local compilation means that you exectute this process on your own machine. This has the advantage that you don’t need write access to the file system on a production server and if you deploy to multiple servers you can do this process only once. Not needing to precompile your assets on the server if assets don’t change is another benefit of precompiling locally.

For that to work, you are responsible of having the compression and minifying gems ready on your machine. You will also be able to put these compiled assets into your Git repo—or whatever version control tool you prefer—and deploy only these final assets to production.

For better performance and speed, it would be nice to have as few files as possible while compressing their size to their minimum. Since servers are highly specialized fellas, we should not expect that they do all the work for us automatically. Precompiling assets is one these things we need to do before we send them our pure CSS, HTML and JS. They should not need to know how to deal with high-level languages like Sass, Slim and whatnot. They have enough responsibilities already.

Rails offers you a Rake task that takes care of precompiling assets though. Such a task is simply a series of predefined steps that need to be executed—in order—to achieve a specific goal using Rake. You can easily write your own tasks in Rake yourself in Rails. 

So when you run

###### Terminal

``` bash

rake assets:precompile

```

Sprockets will take the assets it can find and pre-processes and compiles them into `public/assets`. It will look something like this:

`your_rails_app/public/assets/application-454840c9c54adb8be789d8ca014fa0a02022a5dcd00523f2dd0469bc990ed0e6.js`

`your_rails_app/public/assets/application-454840c9c54adb8be789d8ca014fa0a02022a5dcd00523f2dd0469bc990ed0e6.js.gz`

`your_rails_app/public/assets/application-e80e8f2318043e8af94dddc2adad5a4f09739a8ebb323b3ab31cd71d45fd9113.css`

`your_rails_app/public/assets/application-e80e8f2318043e8af94dddc2adad5a4f09739a8ebb323b3ab31cd71d45fd9113.css.gz`

You will end up with single assets, manifest files like `application.js` and `application.css`. That way you don’t need to manually link to all your assets by hand in your markup. This long number is the fingerprint that is used to compare files and if their contents might have changed before being cached again. What you can also see is that you get two versions of the same file. The one with the `.gz` file extension is the gzipped version of the assets. gzip is used for file compression and decompression and takes off a bit of the fat that we don’t want to have sent over the wire.

### Manifest Files & Directives

These manifest files themselves include the logic to import all the files in the search path that Rails traverses to collect your assets for you. All your partials will be imported first and compiled into a single authorative file that the browser will use. That’s just a default though and you can change that of course.

Every manifest file has directives which are instructions which determine which files need to be required to build up these manifest files. The order in which they are imported is determined in there as well. Put together, their main job is really to build single files that contain the contents of all the files the directives have access to. Sprockets loads all these files, does the necessary pre-processing and rounds the whole thing off by compressing the result. Pretty darn useful!

For your CSS manifest file, `app/assets/stylesheets/application.css` this looks like the following:

``` css

/* ...
*= require_self
*= require_tree .
*/

```

The JavaScript equivalent `app/assets/javascripts/application.js` looks similar:

``` js

// ...
//= require jquery
//= require jquery_ujs
//= require_tree .

```

As you can see from this example, requiring jQuery first for example is a must if you rely on it within your JavaScript code.

### Pre-Processing / Post-Processing

When you pre-process something like CoffeeScript into valid JS, or Sass into CSS, you also have the option to process these files some more. When you post-process them you can do stuff like compress your compiled assets and get rid of stuff the browser does not care about—white space is a good candidate for example. You have a few options at your disposal, letting Sass deal directly with compression for example or simply using the Rails default of the YUI compressor when dealing with stylesheet but I feel a high-level understanding is more important for now. Just as an aside, Rails even let’s you write and configure your own custom compressor—just sayin’.

## Asset Links

Let’s not forget why the Asset Pipeline is a nice thing to have. It aims at making it easy for you to deal with assets. Writing the styles and behaviours for apps has become increasingly more nuanced and complex—while the tools have also become more joyful to work with. Preparing assets for production and serving them should be at least a bit more trivial and save you some time. Having a bit of automation and conventions to organize them is truly nice and makes your job easy as well. When it’s even rounded off with a few sugary assets links to deal with such assets, happiness levels increase even more. Let’s look at a few of the usual suspects: 

+ `javascript_include_tag`
+ `stylesheet_link_tag` 
+ `image_tag`

Since its extraction, Sprockets did not change the way you can link to your assets and they still work the same way as before. These are convenience methods that take the name of your assets as arguments and figure out the extension names for correlated files themselves. These helper methods not only create the necessary tags for the proper HTML but also take care of linking to the asset files. They are not mandatory of course but still nice to have and very readable too.

###### Some View

``` erb

<%= stylesheet_link_tag "some-stylesheet", media: "all" %>

<%= javascript_include_tag "some-js-file" %>

<%= image_tag "some-image.png" %>

```

??? Converted version example


Let’s look at one of them and see how they work in general:

### image_tag

Images placed in `public/assets/images` directory can be accessed via this convenience method—no need to fiddle around with path names yourself. A good example of “convention over configuration” at work.

###### some.html.erb file

``` ruby

<%= image_tag "some-image.png" %>

```

That would result in the following:

``` html

<img src="http://example.com/assets/some-iamge.png" alt="Some image"  />

```

If activated, Sprockets will serve such files if found. When a file like `some-image.png` is fingerprinted like `some-image-9e107d9d372bb6826bd81d3542a419d6.png` it will be treated the same way.

If you need other directories within `public/assets/images` or within `app/assets/images` to organize your images, something extra for icons or svg files maybe, Rails will have no problem finding them—but you need to add the directory’s name first:

``` ruby


<%= image_tag "icons/some-icon.png" %>

<%= image_tag "svgs/some.svg" %>

```

Additionally you have the following options that might come in handy to link to various assets:

+ asset_path
+ asset_url
+ image_path
+ image_url
+ audio_path
+ audio_url
+ font_path
+ font_url
+ video_path
+ video_url


The difference between these siblings is that the ```_url``` version gives you the full path, like `http://example.com/assets/application.css` while the ```_path``` version translates to the relative path to an asset,  like `/assets/application.css`.



The Asset Piple line was extracted since Rails 4 and is not a core functionality anymore. Sprockets is now in charge of it. It is enabled by default though.

Enabled by default though

Skip pipeline when you initiate project

Rails 4 makes a couple of decisions for you though. It adds gems for Sass, CoffeeScript and compression

Setting asset compression methods


Your CSS, JS, CoffeeScript, Sass, Slim, etc files are now organized in one central and convenient place, under `app/assets`.

The Sprockts middleware??? is reponsible for serving files in this directory—Sprockets is in charge. `app/assets` is the place to put files that still need preprocessing, like turning Sass files into their equivalent CSS files.

Also, files placed in `app/assets` get precompiled automatically for going into production—before being deployed onto a server.That means that the files in your `assets` folder are never served as-is for production—they are expected to be processed first.

?? require_tree


## Pimped Styles

### CSS & ERB

You can enhance your stylesheets by using ERB in them. All you need to do is add the `.erb` extension to a css file and you are good to go—Sprockets will take care of the rest. So no to surprise for people who have written tons `.html.erb` files of course. It’s nevertheless not a technique that is widely used I believe. I would probably caution to overuse this, since you can take this very far if you start using Ruby too much inside your stylesheets. On ocassion this might come in handy though. These dynamic CSS files should probably not contain too much logic. Feels like a code smell but the damage seems to be contained if you rely on ERB helpers mostly.

### Sass

When you use `sass-rails` you can make use of `url` and `path` helpers for your assets. They are a no-brainer to use. It’s as simple as this:

###### some-stylesheet.css.erb

``` css

.some-class {
  background-image: image-path("some-image.png");
}

.some-class {
  background-image: image-url("some-image.png");
}

```
`image-path("some-image.png")` translates to `"/assets/rails.png"` while `image-url("some-image.png")` will expand into `url(/assets/some-image.png)`—which in turn translates to the full url like `"http://www.example.com/assets/some-image.png"`. These helper methods know exactly where to find your assets—if you put it in the conventional directory, the search path basically. The difference between them is that you have one absolute path and one relative path. A quick reminder. A relative path is the path to a certain file destination from some other file location. Absolute paths give you the location in reference to the root directory.





During the precompilation, Sprockets will interpolate the code used in the CSS or Sass files and output plain `.css` files again—with or without a fingerprint according to your settings of course. 

###### some-stylesheet.css.erb

``` css



```

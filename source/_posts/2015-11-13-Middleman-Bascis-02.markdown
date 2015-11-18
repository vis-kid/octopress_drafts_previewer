---
layout: post
title: Middleman Basics 02
date: 2015-11-13 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

{% img /images/middleman/3Stooges_600_mirrored.jpg %}

### Topics

+ Data Files
+ Pretty URLs
+ Asset Pipleline
+ Project Templates

+ ### Data Files

So you have already learned how to play with a bit of data that you stored in the triple-hyphen-delimited frontmatter section. You can write separate data files in YAML or JSON which you put in a **/data** directory. I guess this is mostly useful if you have more complex sites with data that rarely changes and where you don’t want to maintain that data directly in your HTML.

Let’s say you have the rights to sell all James Bond movies. We could put a list of them in a data file and iterate over them in our view. If we’d need to change or update that data when a new movie is available you’d only need to apply that change in your .yaml or .json data file. I would not recommend doing that for data that is more complex than that—feels very iffy and wrong. Work with a “real” database if you need more dynamic behaviour. 

**/data/bond.yaml**

``` ruby
movies:
- title: "Dr. No"
year:  "1962"
text:  "John Strangways, the British Intelligence (SIS) Station Chief in Jamaica, is killed. In response, British agent James Bond—also known as 007—is sent to Jamaica to investigate the circumstances. During his investigation Bond meets Quarrel, a Cayman fisherman, who had been working with Strangways around the nearby islands to collect mineral samples. One of the islands was Crab Key, home to the reclusive Dr. No."
image: "bond_movie_01.png"
- title: "From Russia with Love"
year:  "1963"
text:  "SPECTRE's expert planner Kronsteen devises a plot to steal a Lektor cryptographic device from the Soviets and sell it back to them while exacting revenge on Bond for killing their agent Dr. No; ex-SMERSH operative Rosa Klebb is in charge of the mission. She recruits Donald "Red" Grant as an assassin and Tatiana Romanova, a cipher clerk at the Soviet consulate in Istanbul, as the unwitting bait."
image: "bond_movie_02.png"
- title: "Goldfinger"
year:  "1964"
text:  "Bond is ordered to observe bullion dealer Auric Goldfinger: he sees Goldfinger cheating at cards and stops him by distracting his employee, who is subsequently killed by Goldfinger's Korean manservant Oddjob. Bond is then instructed to investigate Goldfinger's gold smuggling and he follows the dealer to Switzerland. Bond is captured when he reconnoitres Goldfinger's plant and is drugged; he is taken to Goldfinger's Kentucky stud farm and is imprisoned. He escapes briefly to witness Goldfinger's meeting with U.S. mafiosi, who have brought the materials he needs for an operation to rob Fort Knox."
image: "bond_movie_03.png"
...
```

**source/bond-movies.html.erb**

``` erb
<h2>Bond movies</h2>
<ol>
<% data.bond.movies.each do |movie| %>
<li>
<%= image_tag movie.image %>
<h3><%= movie.title %></h3>
<h6><%= movie.year %></h6>
<p> <%= movie.text %></p>
</li>
<% end %>
</ol>
```

Forgive my habit of unnecessarily aligning even these little things—I feel dirty if its all rugged up. Anyway, one of the advantages of these data files is surely that they aren’t very susceptible to getting hacked. Even your **/data** directory with all the YAML or JSON data won’t get pushed to your branch that is responsible for hosting—like your **gh-pages** branch if you host your Middleman app on GitHub Pages. During the **build** phase, your data gets injected into your templates localy before it gets deployed. After that, the data in your views is just plain static HTML. Pretty cool!

A word about naming here. When you have data files in a **data** directory you get access to a **data** object. Middleman then creates “objects” for every **.yml**, **.yaml** or **.json** file which you can access through the inital **data** object by chaining it on. Lastly, the object stored in your data files are added on to that—which in turn give you access to the attributes you have stored on that object. In our case, we have a movies YAML “object” with the attributes **title**, **year**, **text** and **image**.


``` erb
<%= data.data_file_name.yaml_or_json_object.attribute %>

<%= data.bond.movies.title %>
```

If you have subdirectories, you just need to tuck them on. Let’s say you have your bond movies data file under a **spy_movies** directory (**/data/spy_movies/bond.yaml**) You’d now access it like so:

``` erb
<%= data.spy_movies.bond.movies.title %>
```

Lastly I should add that storing it in JSON might be cooler but all the excess commas, brackets and braces turn me off tbh. Not only in data files but in frontmatter sections as well. Up to you what suits you best of course. See for yourself:

**some_file.yaml**

``` yaml
bond_girls:
- Strawberry Fields 
- Jill Masterson
- Tiffany Case
```

**some_file.json**

``` javascript
{
	"bond_girls": [
		"Strawberry Fields",
		"Jill Masterson",
		"Tiffany Case"
			]
}
```
+ ### Pretty URLs

If you have a file like **source/bond-movies.html.erb** it will end up as http://appname.com/bond-movies.html. During the build process the we lose the **.erb** file extension and end up with the final **html** version of that page which is mirrored in the URL. That’s alright, normal stuff. For fancier URLs like http://appname.com/bond-movies we gotta work a little.

You need to activate the **Directory Indexes** extension in your config.rb. This creates a folder for every **.html** file. During **middleman build** the finished page ends up in that folder as the index file of that folder—meaning that as an index file it won’t need to show up in URL. When you looked closely, you might have already seen that with the standard **index.html** file that gets created for every Middleman project. It also does not show up in the final URL. Fire up your server and see for yourself.

**config.rb**

``` ruby
activate :directory_indexes
```

Let’s see what happened to your **bond-movies.html.erb** file if you activated that extension. Middleman created a **build/bond-movies** folder and your original filename changed to **index.html**. **build/bond-movies/index.html**.

**Shell output**

``` bash
create  build/bond-movies/index.html
```

There is one little caveat though. Before you activated pretty URLs you could rely on using the assets path. Now with directory indexes in place you need to supply assets with their full absolute path. So calling an image just by its name for example won’t fly anymore. 
????
????
????

If for some reason you want to override the extension for particular file you can.

**config.rb**

``` ruby
page "/bond-movies.html", :directory_index => false
```

**Shell output** if you change it back for **bond-movies.html.erb**:

``` bash
create  build/bond-movies.html
remove  build/bond-movies/index.html
remove  build/bond-movies
```

Now its URL business as usual for that file again. (http://appname.com/bond-movies.html)

Btw, you can opt-out of the directory index naming scheme locally in your individual pages’ frontmatter as well.

**source/bond-movies.html.erb**

``` erb

---
directory_index: false
---

<h1>Bond movies</h1>
...

```

If you wanna build that structure with folder and their respective index files yourself, Middleman is not gonna mess with you. It functions the same way and middleman ignores them if you mix and match that approach.

+ ### Asset Pipleline

I wanna cut to the chase with this one and only show you the pieces that I think are really relevant about the asset pipeline. You can do stuff with Bower and Compass for example but since I’m personally not a huge fan I spare us both the time. O.K. then.

The “asset pipleline” is Rails lingo imported into Middleman and under the hood a gem called [Sprockets](https://github.com/sstephenson/sprockets) does all the heavy lifting. It helps you with handling dependency management, combining assets plus with concatenation and minifyfication—which helps with slimming down your assets. A few helper methods to concisely reference assets are also at your disposal. Beyond that you are also provided with the means to write Sass and CoffeeScript code—right out of the box. Awesome! 

### Concatenation

Concatenation is one of the most important features of the asset pipline. Instead of having a lot of separate HTML requests for every CSS and JS file, you can reduce them drastically by concatenating them into one or a handful of files. The fewer requests you cause the faster your application will load. Simple math. 

### JS Concatenation

By default, Sprockets will press all JS files into a single **.js** file. After **middleman build** this file will be under **/build/javascripts/all.js**. The same goes for your CSS. After build you’ll have all Sass files concatenated together in **build/stylesheets/all.css**.

You combine you JS assets by using partials—whoose filenames start with an underscore—for all your JS files and then **require** them at the very top in your **source/javascripts/all.js** file (I added a **.coffee** extension but it works exactly the same). As you can imagine order does matter for this process.

**source/javascript/all.js**

``` javascript

//= require "_jquery"
//= require "_lib_code"
//= require "_animations"

```

**Screenshot**:

{% img /images/middleman/basics_02/source_javascripts_screenshot.png %}

When you take a look into your new **/build** directory, you’ll only find one **.js** file under **/javascripts** which “digested” the rest of your files—namely **all.js**. 

**Screenshot**:

{% img /images/middleman/basics_02/build_javascripts_screenshot.png %}

### CSS Concatenation

For your Sass code its bascially the same—except that you should use Sass’s **@import** instead of require from Sprockets. Again, place it at the very top and order matters here as well. Unlike requiring JS partials, you leave of the underscore from the partial name for Sass imports.

**/source/stylesheets/all.css.scss**

``` sass

@import 'normalize';
@import 'header';
@import 'navigation';
@import 'footer';

```

**Screenshot**:

{% img /images/middleman/basics_02/source_stylesheets_screenshot.png %}

**source/stylesheets.all.css.scss**

**Screenshot**:

{% img /images/middleman/basics_02/build_stylesheets_screenshot.png %}

### Compression / Minification

Another cool feature of sprockets is compression of these files—also called minification. This process cuts out a lot of the fat like getting rid of unnecessary whitespace and comments. People also call this process *uglify*—and of course there is a gem called [uglifier](https://github.com/lautis/uglifier) which does a beautiful job at this. Names like this makes me love programming even more. Compared to JS asset minification CSS uglification is not that complicated. Behind the scenes , the JS part is a bit more involved and needs more finesse.

To get started you’ll need to add the following to your **config.rb** file. Actually you just need to uncomment these lines under your **:build** block. 

``` ruby

configure :build do
activate :minify_css
activate :minify_javascript
end

```

The next time you use **middleman build** the assets in your **/build** folder will all by uglified and slim. Below are two small examples how this code actually ends up looking:

**Minified CSS under /build/stylesheets/all.css**:

``` css

body{background-color:#d0e4fe}h1{color:orange;text-align:center}p{font-family:"Times New Roman";font-size:20px}

```

**Minified JS under /build/javascripts/all.js**:

``` javascript

switch((new Date).getDay()){case 0:day="Sunday";break;case 1:day="Monday";break;case 2:day="Tuesday";break;case 3:day="Wednesday";break;case 4:day="Thursday";break;case 5:day="Friday";break;case 6:day="Saturday"}

```

Without the asset pipleline you’d have to set up your own thing to write your JS and CSS via a higher-level language like CoffeeScript and Sass. I believe for beginners this setup part can be a pain and therefore the asset pipleline helps newbies to get going. Doing this by hand is a good exercise and important to grasp—but not right away. CoffeeScript and Sass are supported by default.

Awesome, faster websites with fewer requests right out of the gate without any extra work. Take that 2010!

### Asset Pipeline Helpers

For your Sass files you have four helpers at your disposal:

+ image_path()
+ font_path()
+ image_url()
+ font_url()

Because you followed conventions so far you can use these helpers to prepend the correct directory path to your assets. 

Some Sass file:

``` sass
image_path('logo.png')
# => images/logo.png

image_path('nested_folder/some.png')
# => images/nested_folder/some.png

```

### Import Path

The asset pipeline uses *import paths* via Sprockets for your assets. By default **:js_dir** and **:css_dir** are already added to that path. That means that files put in **/source/javascripts** and **/source/stylesheets** are available and automatically imported. On the other hand, if you have assets that you wanna keep in other directories you can also add them to the import path like this:

**config.rb**

``` ruby

sprockets.append_path '/some/other/assets_folder/'

```

In this example, now other assets in **source/some/other/assets_folder/other.css** are also at Middleman’s disposal via this path. Same goes for **.js** files as well of course.

+ ### Project Templates

Middleman comes with a couple of handy project templates that you should at least know about. These templates give you a good starting point when you initiate a new Middleman app for example. You can add these templates at any time later as well though.

+ SMACSS Template
+ Blog Template(via a gem)
+ Mobile Boilerplate Template
+ HTML5 Boilerplate  Template

You can use them like this:

**Shell**

``` bash

middleman init your_fancy_app --template=smacss

```
The template will provide you with all the files and folders that it needs. If you already have an app and want to add a template you use the same command without mentioning your app’s name. Same deal:

``` bash

middleman init --template=smacss

```

Now comes my favorite part of Middleman. Its super straightforward to build your own templates and reuse them whenever you like. You start by createting a **~/.middleman** folder in your root directory. Within that directory you create new folders for your templates. For example **/.middleman/podcast** would be a *podcast* template. Then you fill this podcast dirctory with all the files and folders you need. For example, if you want to have additional stylesheets available for your Middleman app then you need to simulate Middleman’s filepath to make it super easy to use them.

In the screenshot below I have prepared a dummy example that has a couple of files that I might need for every project and put them in a **bourbon** folder. Now I have a bourbon template.

**Screenshot for ~/.middleman/bourbon**:

{% img /images/middleman/basics_02/bourbon_template_screenshot.png %}

Since I simulated Middleman’s file structure, these stylesheets will show up exactly where I need them after I initiated that template. They are now under **/source/stylesheets** also ready to be imported into my **/source/stylesheets/all.css.scss** file. 

**Screenshot for /middleman_app/source/stylesheets**:

{% img /images/middleman/basics_02/source_bourbon_screenshot.png %}

Since I already made my template styles partials its business as usual:

**source/stylesheets/all.css.scss**

``` scss

@import 'bourbon_mixins/mixins';
@import 'bourbon_neat/grids';
@import 'bourbon_refills/cards';
...

```

Boom! Finished! One thing you should be careful about though. When we use **middleman build** now to create our new **build** directory these files will get absorbed by **all.css** and none of the bourbon template folders will show up there. However, if you forget to have a leadgin underscore in your filenames for these styles, the complete folder will transfer into **/build**—with the prospective **.css** files of course. The **@import** statements in **all.css.scss** didn’t make a difference. 

If you have a ton of templates and wanna just check the list for a name you can use the following command:

**Shell**:

``` bash
middleman init --help

# =>  # Use a project template: default, html5, mobile, smacss, bourbon

```

In case you wanna reinvent the wheel, take a look at these open sourced [templates](https://directory.middlemanapp.com/#/templates/all/). If you have never played much with templates, I recommend initiating a dummy app and take them for a spin. See what files get created or overwritten. Poke around a little bit. Then build a dummy folder with a couple of Sass files for a template under **~/.middleman** and see what happens when you initiate this template. Nothing beats learning by doing these little experiemnts along the way!


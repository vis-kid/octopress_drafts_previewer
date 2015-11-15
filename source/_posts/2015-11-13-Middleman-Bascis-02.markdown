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

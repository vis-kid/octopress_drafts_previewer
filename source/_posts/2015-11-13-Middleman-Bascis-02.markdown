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

``` erb
<h2>Bond movies</h2>
<ol>
  <% data.bond.movies.each do |movie| %>
    <li>
          <%= image_tag movie.imge %>
      <h3><%= movie.title %></h3>
      <h6><%= movie.year %></h6>
      <p> <%= movie.text %></p>
    </li>
  <% end %>
</ol>
```

Forgive my habit of unnecessarily aligning even these little things—I feel dirty if its all rugged up. Anyway, one of the advantages of these data files is surely that they aren’t very susceptible to getting hacked. Even your **/data** directory with all the YAML or JSON data won’t get pushed to your branch that is responsible for hosting—like your **gh-pages** branch if you host your Middleman app on GitHub Pages. During the **build** phase, your data gets injected into your templates localy before it gets deployed. After that, the data in your views is just plain static HTML. Pretty cool!

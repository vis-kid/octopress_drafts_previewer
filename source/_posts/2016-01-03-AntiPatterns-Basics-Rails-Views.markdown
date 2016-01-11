---
layout: post
title: AntiPatterns Basics—Rails Views
date: 2016-01-03 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Views/funky-bridge.gif %}

## Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

This one is exactly written for you if all this sounds rather new to you and you identify yourself as being more on the beginner side of all things Ruby / Rails. I think, it’s best if you approach these articles as quick skinny-dips into a much deeper topic whose mastery will not happen overnight. Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

## Topics

+ Rails Views
+ PHPitis
+ Extracting Helpers
+ Helpful Helpers
+ Forms
+ Partials
+ Conditional Content
+ Semantic Markup

## Rails Views

Rails comes with ERb out of the box and I think it’s not necessary to throw in cool view-rendering engines like Slim for our examples for now. When you think “convention over configuration” mostly applies to the model and controller layers you are missing out on a lot of the goodies that makes working with Rails so speedy and progressive. Taking good care of the view layer includes not only the way you compose your markup but also CSS / Sass, JavaScript, view helpers and your layout templates. Viewed from that angle it becomes a bit deeper than you might think at first. Alone that number of technologies which can be involved in creating your views suggest that care should be taken to keep things neat, clear and flexible. 

Since the way we write markup and styles is a lot less constrained than domain modeling, you want to be extra cautious to keep things as simple as possible. Maintenance should be pretty much your number one priority. Since redesigns or design iterations can be more frequent than extensive changes to your model layer, preparing for change gets a whole nother meaning when it comes to your user facing layer. My advice, don’t necessarily build for the future but also, by all means, do not underestimate the rate of change—especially if you have one of those “idea guys” who knows jack about implementations on the team. What I like about Rails’ approach towards views in MVC is that it is treated as equally important and the lessons learned from the model domain were incorporated into the view—whenever possible and useful. Other frameworks seem to agree since they integrated a lot of these ideas pioneered by Rails.

Since the last article was a bit more extensive I chose this topic as a small breather. The following articles about Rails controllers and testing are again bigger in size. The AntiPatterns for views are not that many but they are nevertheless equally important—at least. We’ll focus on the main one, PHPitis, and work through a couple of techniques to keep your views lean and mean. Since the view is your presentation layer, maybe you should be especially careful to not create a hazardous mess. Let’s get to it!

## PHPitis

Why do we have MVC in the first place? Yes, because the separation of concerns seemed like the most reasonable thing to do. Sure the implementations of this idea vary a bit here and there but the overall concept of having distinct responsibilities for each layer is the core motivation for building robust applications. Having tons of code in your view layer might not be alien to developers coming from the PHP side of things—although I hear their frameworks have caught up already (heavily influenced by things like Rails?)—but in Ruby land these things have been a loudly voiced AntiPattern—since forever I feel like.

The obvious problems like mixing responsibilities and duplications aside, it simply feels nasty and lazy—a little stupid too to be frank. Sure, I get it, when you develop much within a framework, language or whatever ecosystem, it’s easy to become complicit or numb towards crap like that. What I like about the people pushing Ruby is that these things seem to have a lot less weight—might be a reason why innovating never seemed to be a problem within the community. Whatever works best wins the argument and we can move forward.

So is this a whole section dedicated to bash PHP? Not at all! In the past, PHP apps had the reputation of having weak separations between models, views and controllers (Maybe this was one of the core reasons why people felt writing apps with Rails was much more appealing). Having single files with code for all three layers didn’t seem that sexy. So when we stuff tons of Ruby / domain code into our views it starts to look like the dreaded PHP style of structuring things—PHPitis. Only a few things are as bad as this when it comes to developing web apps I feel. When you care about happy developers and your own future sanity, I can’t see why anyone would go down that road—only pain ahead it seems.

Rails offers a lot of goodies to minimize code in the view as much as possible. You must learn the ways of helpers, layouts and preprocessors in order to achieve a cleaner view. A simple rule of thumb is to keep domain logic out of your views—no shortcuts! The price to pay for being lazy on this is hard to overestimate. The Ruby code that must be in the presentation layer should be as little and as simple as possible as well as intelligently organized. Your reward will be code that is a joy to extend and to maintain—new team members will also have an easier time wrapping their heads around the new codebase. As a bonus, neat freak designers who code also won’t be angy and hide rotten food in your salad if you keep tons of Ruby code out of their markup.

### Helpful Helpers

Knowing the myriad of helper methods in Rails will significantly improve the quality of your presentation layer. Not only will it clean things up and inject the occasional speed boost in productivity, but more importantly it helps you fight PHPitis. The thing that you should appreciate about these helpers is that they represent extractions from commonly needed code. Instead of reinventing the wheel, when in doubt, check if there isn’t already a helper around that solves your issue in the view—same goes for Models and Controllers as well of course.

Here’s a list of helpers you should look into pretty much right away:

+ ```form_for```
+ Other helpers for forms.
+ ```fields_for```
+ ```link_to```
+ ```content_for```
+ And writing your very own of course.

### Forms

Let’s have a look at ```form_for``` first. I know forms are a little bit boring and not that sexy for a topic, but I highly encourage you to read up on them to familiarize yourself with the finer details. It’s important to understand how they work. I remember often just glancing over them without giving them much attention. Sure you can get them to work quite easily without understanding what’s going on under the hood. In the future, I might take the time to write a complete article on them. In the meantime, I highly recommend that you spend a little time checking the documentation–at least you‘ll appreciate how convenient Rails makes it to deal with form stuff.

#### The Ugly

The example below shows you the HTML of a little form we need for creating agents. It only accepts three parameters as input: ```name```, ```number``` and ```licence_to_kill```. A lot of code for this little task actually. The ```authenticity_token``` comes from Rails–it’s a security thing that protects the app from “cross-site request forgery”.

###### some.html.erb

``` html

<form class="new_agent" id="new_agent" action="/agents" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="8tD8G9mBt5l34TcN6Tnt/Mbto32itOuS1fWbc3Ez1vKinUmdexxUQlFp7mXIYoazQJjjSgryFIuL4dioxtJw/g==" />

  <label for="agent_name">Name</label>
  <input type="text" name="agent[name]" id="agent_name" />

  <label for="agent_number">Number</label>
  <input type="text" name="agent[number]" id="agent_number" />

  <label for="agent_licence_to_kill">Licence to kill</label>
  <input name="agent[licence_to_kill]" type="hidden" value="0" /><input type="checkbox" value="1" name="agent[licence_to_kill]" id="agent_licence_to_kill" />

  <input type="submit" name="commit" value="Create Agent" />

</form>

```

Writing a form by hand is not only lengthy but error prone as well. Also, if we would approach it that way, we’d also have to solve the issue with the varying routes and  CSS classes that we might need for creating a new object and updating an existing one—in effect, we would need to duplicate forms to create and edit records. As you’ll see soon, Rails meets you more than halfway on that. Verdict, however you put it, the manual approach is nasty and lacks convenience.

#### The Bad

We could go down the following road which does not make perfect use of conventions in Rails. Heads up, don’t do it. It basically shows that you don’t handle the available tools to your advantage and you are duplicating the form for ```new``` and ```edit``` actions.

###### some.html.erb

``` erb

<%= form_for :agent,
             url: agents_path(@agent),
             html: {method: :post} do |form_object| %>

  <%= form_object.label      :name %>
  <%= form_object.text_field :name %>

  <%= form_object.label      :number %>
  <%= form_object.text_field :number %>

  <%= form_object.label      :licence_to_kill %>
  <%= form_object.check_box  :licence_to_kill %>

  <%= form_object.submit 'Create New Agent' %>

<% end %>

```

What happens here is that the form builder carries the model you need for the form.

``` erb

<%= form_object.text_field :name %>

```

Behind the scences, the line above get’s expanded into the following:

``` erb

<%= text_field :agent, :name %>

```

The ``` form_for``` method takes a couple of arguments:

+ A symbol or a string for specifying the object
+ A ```url``` hash
+ A ```html``` hash.
+ A ```namespace``` hash

The url hash is for specifing the routing options. That means that you can manually specify to which routing path you submit the form to—named routes come in handy with this. This style is called the “generic way” because you need to manually configure the ```form_for``` call. Why is this solution suboptimal? Because we want to keep business logic out of our Views and Controllers as much as we can. A side effect of that is that we need to change fewer parts when needed.

###### HTML

``` html

<form action="/agents" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="FUjXyB+EKkwElmRNt//pUmzYa95qy+cBQWcUYJtOHIFFBWJOvRnJlyIevSWWpIId6q0r6cKNGBgfc1e7LK+6jQ==" />

  <label for="agent_name">Name</label>
  <input type="text" name="agent[name]" id="agent_name" />

  <label for="agent_number">Number</label>
  <input type="text" name="agent[number]" id="agent_number" />

  <label for="agent_licence_to_kill">Licence to kill</label>
  <input name="agent[licence_to_kill]" type="hidden" value="0" /><input type="checkbox" value="1" name="agent[licence_to_kill]" id="agent_licence_to_kill" />

  <input type="submit" name="commit" value="Create New Agent" />

</form>

```

In case you missed it, this approach did not provide us with ids and classes for the ```form``` tag automatically. The ones for ```input``` tags however were generated for you. We’ll fix that in a minute. Just know what you can get for free and that you probably should use this to your advantage. If you need something different or an additional namespace, you can use the ```html``` hash or the ```namespace``` hash to specify things a bit more.

###### some.html.erb

``` erb

<%= form_for :agent,
             url: agents_path(@agent),
             html: {method: :post, class: 'create_agent', id: 'unique_agent'},
             namespace: 'mi6' do |form_object| %>

```

###### HTML

``` html

<form class="create_agent" id="unique_agent" action="/agents" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="IAkMk58AJeTMbNHLf8wzYwf+seSpC8OfoHIJuu8M80FwRLkVPZ3GP+rkCKNel1gsgYvx0wFNPIb+ZkphWO1VTQ==" />

  <label for="mi6_agent_name">Name</label>
  <input type="text" name="agent[name]" id="mi6_agent_name" />

  <label for="mi6_agent_number">Number</label>
  <input type="text" name="agent[number]" id="mi6_agent_number" />

  <label for="mi6_agent_licence_to_kill">Licence to kill</label>
  <input name="agent[licence_to_kill]" type="hidden" value="0" /><input type="checkbox" value="1" name="agent[licence_to_kill]" id="mi6_agent_licence_to_kill" />

  <input type="submit" name="commit" value="Create New Agent" />

</form>

```

Not bad! Now the ```form``` tag has the specified class and id—whatever makes your blood flow—and the ```input``` tags are namespaced with ```mi6```. Almost there.


#### The Good

This one is called the “resource-oriented style” and has the least amount of Ruby you need to write in your views. With that approach we want to rely on automated resource identification. Rails figures out which routes it needs based on the object itself. Not only that, it gives you a different HTML output for creating a new object or for editing an existing one. Behind the scenes, Rails just asks the object if it already exits and acts accordingly. Creating forms this way is a clever use of conventions and helps avoid duplication. One line and all the heavy lifting is done for you.

###### some.html.erb

``` erb

<%= form_for @agent do |form_object| %>

  <%= form_object.label      :name %>
  <%= form_object.text_field :name %>

  <%= form_object.label      :number %>
  <%= form_object.text_field :number %>

  <%= form_object.label      :licence_to_kill %>
  <%= form_object.check_box  :licence_to_kill %>

  <%= form_object.submit %>

<% end %>

```

Much better, isn’t it? Now we get exactly what we need for both existing and new objects. Also, we didn’t need to add text to our submit button. Rails took care of that and also adapts to new or existing objects.

###### HTML FOR NEW OBJECT

```html

<form class="new_agent" id="new_agent" action="/agents" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="4BwAH2OkTvD0hOPOO7NB6rT94PoENUI4MazyoY8RN7+wUbWZwTmtK9IMOqYa6CqlMoigzaxzvSFvuLF6OPCRsw==" />

  <label for="agent_name">Name</label>
  <input type="text" name="agent[name]" id="agent_name" />

  <label for="agent_number">Number</label>
  <input type="text" name="agent[number]" id="agent_number" />

  <label for="agent_licence_to_kill">Licence to kill</label>
  <input name="agent[licence_to_kill]" type="hidden" value="0" /><input type="checkbox" value="1" name="agent[licence_to_kill]" id="agent_licence_to_kill" />

  <input type="submit" name="commit" value="Create Agent" />

</form>

```

###### HTML FOR EDITING OBJECTS

``` html
<form class="edit_agent" id="edit_agent_7" action="/agents/7" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="4BwAH2OkTvD0hOPOO7NB6rT94PoENUI4MazyoY8RN7+wUbWZwTmtK9IMOqYa6CqlMoigzaxzvSFvuLF6OPCRsw==" />

...

```

When editing objects, it is also reflected in the HTML output by adding the id to the form id and the route / action needed for updating a specific object.

 A word about Rails magic. When people argue in exactly these situations that Rails is too magical for their taste, I often think that this could simply mean that they haven’t spent enough time learning the tools of their trade. Because once you take the time to master these tools, you’ll often understand not only why a simplification or an extraction was made but they also appear a lot more sober and straightforward.

#### Attention!

The code examples above used ```form_object``` as a block parameter. This is not recommended best practice but was done to remind you what this object represents and what gets yielded from ```form_for```. Most people just use a plain ```|f|``` or ```|form|```—which looks much nicer and concise. Btw, stuff like ```label```, ```text_field```, ```check_box``` and the likes are just helper methods that are called on the form builder object. There are a ton of them which cover pretty much any possible need you might encounter. 

###### some.html.erb

``` erb

<%= form_for @agent do |f| %>

  <%= f.label      :name %>
  <%= f.text_field :name %>

  <%= f.label      :number %>
  <%= f.text_field :number %>

  <%= f.label      :licence_to_kill %>
  <%= f.check_box  :licence_to_kill %>

  <%= f.submit %>

<% end %>

```

Concise and reads nice, right?

### Partials

Collections are another thing we don’t want to be too verbose about. Rendering partials for individual objects of collections is so concise and straightforward—if done right—that I feel you have very little excuse not to make use of Rails’ conventions to reduce Ruby view code. Let’s turn things around with this one and start with an example that shows you how you are encouraged to approach this. Along they way, I’ll explain what you can leave out as well.

#### The Good

###### app/views/agents/index.html.erb

``` erb

<%= render @agents %>

```
The ```render``` method is quite smart. The line above is all you need to write for iterating over a collection. If you need to change something in this view, it will be a very small change—and therefore a small cause of error. What happens here is that the framework is able to determine which partial it needs. Through the name of the object, it knows where to look for the partial—given that you adhere to the conventional naming of things. The way I see it, this is a good example that Rails is not trying to impress you with wizardry. The Rails team works hard to make your lives easier by cutting through repetitive red tape of sorts.

###### app/views/agents/_agent.erb

``` erb

<h3>Agent name: <%= agent.name %></h3>
<h4>Licence to kill: <%= agent.licence_to_kill %></h4>
<h4>Number: <%= agent.number %></h4>
<h4>Gambler: <%= agent.gambler %></h4>
<h4>Womanizer: <%= agent.womanizer %></h4>

```

The only other thing that is necessary to make this work is placing a partial template at the appropriate path in your objects’s directory and extract the attributes you need from the object. No need to write any loops on your own. Fast and easy, handy and pragmatic I’d say. This extraction was originally done because the name of the partial was most of the time the name of the iterated object anyway and so it was easy to create a convention that handles this common task more effectively.

#### The Bad

Ok, now that we know how to handle this, let’s look what you could do and should avoid. The example below is just a bad usage of Rails but I wouldn’t call it ugly this time.

###### app/views/agents/index.html.erb

``` erb

<% @agents.each do |agent| %>
  <h3>Agent name: <%= agent.name %></h3>
  <h4>Licence to kill: <%= agent.licence_to_kill %></h4>
  <h4>Number: <%= agent.number %></h4>
  <h4>Gambler: <%= agent.gambler %></h4>
  <h4>Womanizer: <%= agent.womanizer %></h4>
<% end %>

```

You get the same result as above but it’s definitely more verbose. Iterating over the collection in the view is not necessary anymore. When you use ```render``` as above, the block parameter ```agent``` is implied and you can just use it without the ```each``` loop. So, stay away from code like this—it does not make you look particularly good (but nobody will collect your head for it either). It’s just not elegant and adds to the PHPitis.

### Extracting Helpers

The most obvious solution to clean up code from your views is avoiding to write any or extracting them intelligently. Let’s say we want to scramble the names of our agents in the index list. We should not put this code directly in our views. If we decide that the model is also not the appropriate layer to place this, then a custom helper in the ```app/helpers``` directory might be the right choice.

##### app/helpers/agents_helper.rb

``` ruby

module AgentsHelper
  def scramble(agent)
    agent.name.split('').shuffle.join
  end
end

```

By packaging this in a module inside the helpers directory we now have access to this method in our views. Please give specific helpers their own home and don’t put everything on ```ApplicationHelper``` (```app/helpers/application_helper.rb```) which is really meant for more “global” stuff.

Now I can access this little fellow in my partial template—or any view—for rendering my collection of agents. 

##### app/views/agents/_agent.erb 

``` erb

<h3>Agent name: <%= scramble(agent) %></h3>
<h4>Licence to kill: <%= agent.licence_to_kill %></h4>
<h4>Number: <%= agent.number %></h4>
<h4>Gambler: <%= agent.gambler %></h4>
<h4>Womanizer : <%= agent.womanizer %></h4>

```

Your own custom helpers are a great way to keep your views clean and healthy. And as you have seen, it’s so quick and easy that there’s little excuse to be too lazy and not extract them for battling PHPitis.

## Conditional Content

The helper method ```content_for``` is a handy tool for extracting content that doesn’t really fit the bill for a partial but needs a bit of encapsulation. It’s a way to store a bit of markup that you can apply on a page per page basis—you yield it into the layout where needed. In size, it should be a lot smaller than partials or even layouts.

This technique can also save you the step to create your own method for it. Navigational menues or sidebars are often examples where this helper becomes useful. Let’s say you want to have a spot in your menu that is only for admins but don’t need to adjust the whole layout. Or you have pages where the sidebar is not needed. With ```content_for``` you inject what you need where you need it on a page per page basis. Duplication no more!

###### app/views/agents/index.html.erb

``` erb

<% content_for :double_o_navbar do %>
  <li><%= link_to 'Operations', operations_path %></li>
  <li><%= link_to 'Agents', agents_path %></li>
  <li><%= link_to 'Messages', messages_path %></li>
<% end %>

<%= render @agents %>

```

###### app/views/layouts/application.html.erb

``` erb

...

<body> 
  <header>
    <ul class='navbar'>
      <li><%= link_to 'Home', root_path %></li>
      <li><%= link_to 'About', '#' %></li>
      <%= yield :double_o_navbar %>
    </ul>
  </header>
  
  <%= yield %>

</body>
</html>
```

Aside from that fact that this ```header``` is a good candidate for extraction into a partial, look at the ```yield :double_o_navbar``` section. This is a yielding region that inserts code from a ```content_for``` block. It will only insert code if the symbol names match. Here we want only double-o agents to have access to certain links in the navbar. Everyone else sees just `Home` and `About`. Think about the special links an admin needs to see that should never face a public interface.

You can also use this helper to insert ```id``` or ```class``` attributes on HTML tags if needed. Every once in a while this comes in handy.

Another common use is populating the ```<title>``` of a page dynamically with a ```content_for``` block.

###### app/views/layouts/application.html.erb

``` erb

<title>
  Spectre – <%= yield(:title).presence || "Default" %>
</title>

```


###### some.html.erb

``` erb

<% content_for :title do %>
  Some funky title
<% end %>

```

You just place the title you want in a ```content_for``` block and the application layout will insert it for you. You can get more clever with it but that should suffice for now. Should you have no need for a title or forget to add one then the logical ```||``` will kick in and yield a default of your choice. In the example above we need to check for the presence of a title or the default won’t work.

What you definitely don’t wanna do is create instance variables for that kinda thing. Single responsibilities, remember?

``` ruby

def show
  @title = "Some page title"
end

```

One more thing, you can ask if pages have a ```content_for``` block.

###### app/views/layouts/application.html.erb

``` erb

<% if content_for?(:q_navbar) %>
  <%= yield :q_navbar %>
<% end %>
```

This can help you avoid duplicating markup that is relevant to styling a page which adapts if elements are present on a page or not.

## Semantic Markup

This is stuff you definitely want to avoid.

``` html
<div class='container'>
  <div class='col-lg-6'>
    <div class='col-md-4 col-md-offset-2'>

```

The markup above is from the [bootstrap](http://getbootstrap.com) documentation and specifies how the columns are supposed to 'look'—information that has no semantic meaning and actually belongs into your stylesheets. That’s the stuff designers have nightmares about.

So what’s the deal with that? This is important because—besides the questionable naming of classes—unsemantic markup violates **separation of concerns**. Your markup should not be bothered with styling information, instead both should stand on their own and enable you to **switch out styles effortlessly**—without touching your HTML. It’s not as difficult as it might sound at first. It takes a bit of discipline though. 

When you are able to keep that styling information out of your markup you have effectively achieved reducing PHPitis on another front—for designers an essential one! Also, the use of **generic divs** without inherent meaning is another example of poor markup. **HTML5** gives you lots of useful elements that convey more information to your **future self**, **other developers and search engines**. Naming is supposedly hard, but HTML5 provides you with lots of **semantic elements** that make your options much easier in that regard. 

## Final Thoughts

I hope you have seen that Rails Views don’t need much love to shine. Developers can be a bit snobby about the frontend layer. Dealing with Markup sometimes seems to be a little beneath them—writing HTML, DUH! Well, I shouldn’t throw any stones, but I came to appreciate a fine tuned, well honed presentation layer. It makes it much more fun to work with and when done right, much faster to make the inevitable changes. Parsing tons of Ruby code mixed with badly written markup is not a fun experience.

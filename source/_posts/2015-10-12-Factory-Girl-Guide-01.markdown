---
layout: post
title: Factory Girl 101
date: 2015-10-12 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

{% img /images/Factory-Girl-Guide/hine-lewis-national-child-labor-committee-collection.jpg %}

[{% img /images/Factory-Girl-Guide/Factory-Girl-Icon.png  150 %}](https://github.com/thoughtbot/factory_girl)

This two-part mini-series was written for people who quickly wanna jump into working with this gem and cut to the chase without digging through the documentation for themselves. 

I did my best to keep it newbie-friendly for folks who started to play with tests rather recently. Obviously having been in the same shoes made me believe that it doesn’t take much to make new people feel more comfortable with testing. Taking a bit more time to explain the context and demystifying the lingo goes a long way in cutting down frustration rates for beginners imho.

### Contents

+ Intro & Context
+ Fixtures
+ Configuration
+ Defining factories
+ Using factories
+ Associations
+ Inheritance
+ Multiple records
+ Sequences

+ ### Intro & Context

Let’s start with a little bit of history and talk about the fine folks at [thoughtbot](https://thoughtbot.com/) who are in charge of this popular Ruby gem. Back in 2007/2008 [Joe Ferries](https://github.com/jferris), CTO at thoughtbot, had had it with fixtures and started to cook up his own solution. Going through various files to test a single method was a common pain point while dealing with fixtures. Put differently, that practice also lead to writing tests that don’t tell you much about their context being tested right away. 

Not being sold on that practice made him checkout various solutions for factories but none of them supported everything he wanted. After his first attempt, Factory Girl made testing with fixture data more readable, DRY and also more explicit by giving you the context for every test. A couple of years later, [Josh Clayton](https://twitter.com/joshuaclayton), Development Director at @thoughtbot in Boston, took over as the maintainer for the project. Since then the project has grown steadily and has become a go-to solution in the Ruby community 

What’s the main pain point that’s being solved today? When you build your test suite you’re dealing with a lot of associated records and with information that’s changing frequently. You further want to be able to build data sets for your integration tests that are not brittle, easy to manage and explicit. Your data factories should be dynamic and able to refer to other factories—something that is reasonably far beyond YAML fixtures from the old days. Another convenience you want to have is the ability to overwrite attributes for objects on the fly.

Factory Girl allows you to do all of that effortlessly—given the fact that it is written in Ruby and a lot of Metaprogramming witchraft is going on behind the scenes—and you are provided with a great domain-specific language. Building up your fixture data with this gem can be described as easy, effective and overall more convenient. That way you can deal more with concepts than with the actual columns in the database. But enough of talking the talk, let’s get our hands a bit dirty.

+ ### Fixtures

Folks who have experience with testing applications and who don’t need to learn about the concept of fixtures, please feel free to jump right ahead to the next section. This one is for newbies who just need an update about testing data.

Fixtures are sample data—that’s it really! For a good chunk of your test suite you want to be able to populate your test database with data that is tailored to your specific test cases. For quite a while, many devs used YAML for this data—which made it database independent. In hindsight, being independent that way may have been the best thing about it. It was more or less one file per model. That alone might give you an idea about all kinds of headaches people were complaining about. Complexity is a fast growing enemy that YAML is hardly equiped to take on. Below you’ll see what such a **.yml** file with test data looks like.

YAML file: **secret_service.yml**

``` yaml
Quartermaster:
  name: Q
  function: Quartermaster
  skills: inventing gizmos and hacking
  birthday: Unknown

00 Agent:
  name: James Bond
  skills: getting Bond Girls killed and covert infiltration
  birthday: Unknown
```

It looks like a hash, doesn’t it? It’s a colon-separeted list of key / value pairs that are separated by a blank space. You can reference other nodes within each other if you want to simulate associations from your models. But I think its fair to say that that’s where the music stops and many say their pain begins. For data sets that are a bit more involved, fixtures are difficult to maintain and hard to change without affecting other tests

To avoid breaking your test data when the inevitable changes occur, developers where happy to adopt newer strategies that offered more flexibility and dynamic behaviour. That’s where Factory Girl came in and left the YAML days behind. Another issue is the heavy dependency between the test and the fixture file. [Mystery guests](https://robots.thoughtbot.com/mystery-guest) are also a major pain with these kinds of fixtures. Factory Girl let’s you avoid that by creating objects relevant to the tests inline.

Sure, fixtures are fast and I’ve heard people argue that a slow test suite with Factory Girl data made them use fixtures again. In my mind, if you are using Factory Girl so much that it really slows down your suite, you might be overusing factories where they are not needed and you might ignore strategies that are not hitting the database when you create / save objects. You’ll see what I mean when we get to the relevant chapter(s). Of course, use whatever you need / see fit but consider yourself warned if you get burned.

I think it would be fair to add that in the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up data for testing your application. They played an important role and helped moving the industry forward. Nowadays they have a reasonably bad rep though. Times change so let’s move on to factories which aim at replacing fixtures.

+ ### Configuration

I assume you already have Ruby installed on your system. If not, come back after consulting Google and you should be good to go. Its quite straightforward I’d say. You can install the gem manually in your terminal via

**Shell:**
``` bash
gem install factory_girl
```

or add it to your Gemfile 
``` ruby
gem "factory_girl", "~> 4.0"
```
and run `bundle install`.

You are covered of course if you want to use Factory Girl with Rails. The *factory_girl_rails* gem provides a handy Rails integration for *factory_girl*.

**Shell:**
``` bash
gem install factory_girl_rails
```

**Gemfile:**
``` ruby
gem "factory_girl_rails", "~> 4.0"
```

### Convenient Syntax Setup

If you would like to prefer typing something like

**Ruby:**
``` ruby
create(:user)
```

instead of 

``` ruby
FactoryGirl.create(:user)
```

everytime you use one of your factories you just need to include the `FactoryGirl::Syntax::Methods` module in your test configuration file. If you forget that step, you have to preface all Factory Girl methods with the same annoying preface.

For **RSpec** find the **spec/spec_helper.rb** file and add:
``` ruby
RSpec.configure do |config|
  config.include FactoryGirl::Syntax::Methods
end
```
#### Attention!
For the newbies among you, beware that you only have to add the second line which is responsible for including the module. The **RSpec.configure** block will already be there buried under some amount of comments. Easy, you’ll see! You can also do the same by creating a file specifially for Factory girl like this: **spec/support/factory_girl.rb** file. In that case you will have to add the whole config block yourself of course. 

You can use the same configuration if you are using

+ Test::Unit
+ Cucumber
+ Spinach
+ MiniTest
+ MiniTest::Spec
+ minitest-rails

You can go more fancy with your configuration by throwing in **DatabaseCleaner** for example but the documentation does only the step above so I’ll move on from here.

+ ### Defining Factories

You can define your factories anywhere but they will be loaded automatically if they are placed in the following locations:

Test::Unit:
``` bash
test/factories.rb
test/factories/*.rb
```

RSpec:
``` bash
spec/factories.rb
spec/factories/*.rb
```

As you can see you have the option to split them into separate files that adhere to whatever logic or bundle your factories together into one big **factories.rb** file. The complexity of your project will be the best guideline for when to logically separate factories into their own separate files.

### Barebones Factories

Factory Girl provides a well-developed ruby DSL syntax for defining factories like *user*, *post* or any object—not only Active Record objects

You start by setting up a define block in your **factories.rb** file.

**Ruby:**
``` ruby
FactoryGirl.define do
  #define factories here
end
```

All factories are defined inside this block. Factories just need a **:symbol** name and a set of attributes to get started. Here the factory is named **agent** and the attributes are **name**, **function**, **skills** and **birthday**.

**Ruby:**
``` ruby
FactoryGirl.define do

  factory :agent do
    name "Q"
    function "Quartermaster"
    skills "Inventing gizmos and hacking"
    birthday "Unknown"
  end

end
```

#### Attention!

 If you take one thing away from this article then it should be the following: Don’t be lazy and only define the most barebones factory possible in order to be valid by default—valid in the sense of Active Record validations for example. Factory Girl will call **save!** on instances. That means validations are always run. If any of them fail, **ActiveRecord::RecordInvalid** gets raised. Defining only the bare minimum gives you more flexibility if your data changes and will reduce the chances of duplication and breaking tests—since coupling is reduced to the core. 

If you think this sounds hard to manage, you’ll most likely be glad to hear that there is a handy solution in place to segregate objects and their attributes via **Traits**. Its a great strategy to complement your barebones factories and keeps them DRY at the same time. My second article will focus quite a bit on this very rad feature of Factory Girl. 







Notes:
Factory girl is a ruby gem

it was data stored in yaml

have post with cretain attributes
can have associations(references to other factories), callbacks(for you create data), traits(mix in behaviour)


references to related data

overwrite on the fly

attribute order does not matter

uses a lot of metaprogramming with a lot of dynamic method definitions

you can refer to attributes on objects even if you haven’t defined them in your factory. that means you can define factories with the absolute bare minimum to make them valid objects and assign attributes needed for your tests on the fly.


Want to have it DRY

create is actually going to persist the data. the goal is declaring bare bones factories and either have child factories and assign different attribues that mutate the data in a certain manner or that you use traits where you can mutate multiple columns (attributes) and change a handful of columns at at time or you can add associations or different callbacks. 

Being able to define traits as a concept. for example flag on a post. published boolean and then requirements change and you need timestamp. later it changes again because you need state machine with published, not published, draft and so one. Instead of building a factory girl object and putting that column when you create this record ends up being a painfpoint because everything needs to change now when you refer to published-> all through your tests. -> traits just referring to this concept which lets you apply and affect changes in only one central DRY place. Imagine if you’d have used an options hash for instantiation and that requirement totally changed how many potential places in your tests might break and need attention now. with traits your test dont necessarily care about how its stored in the database. 

traits are comma separated list of symbol traits / attributes that you wanna addon to a particular factory. traits are also defined in your factories file(s). traits is a tool for eliminating duplication in your test suite. 

And because you still do (hopefully) unit tests on the columns  for your models that are represented in traits you give them you still give them the same amount of care than the attributes that are needed to have valid objects. 


The more date you put in your global test space the more pain you are gonna have maintainance wise. Don’t be lazy!


Outro

That’s also one more reason why only defining only the absolute basic objects and their attributes goes a long way in avoiding the side effects of global coupling. -> Bare minimum factory definitions and segregating related objects into separate factories or factories witht traits is the key thing to remember whatever you do. 


A lot of times you actually dont need factory girl to write some of your unit tests. Josh would be the first to attest to that. Bigger systems with lots of data and collaborators and stuff that that you dont need for your unit tests. If you write unit tests and your data doesnt need any associations, it doesnt need persistance, you dont care about callbacks, dont use factory girl or at least use build (stubbed??) which instantiates the record and assignes all the attriubutes, gives it an id and raises if you interact with the database at all.  which gives you fake data that looks like its been persisted - gives you a full-fledged object that looks like its been saved. A lot faster. nothing hits the database at all. 

Global coupling. Data is global in your test suite? it can become painful. When you change some attribute and tests start to break. Idea is to declare factories and then segregate them -> Something is different is named differently. A user with admin flag becomes admin in separate factory or trait. Regular user vs admin user -> Different type of users. Splitting up the data types helps ease some of the pain of the fact that they are global. So like changes to admin are less likely to break global things. 


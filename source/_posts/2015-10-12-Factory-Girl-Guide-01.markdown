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

This two-part mini-series was written for people who quickly wanna jump into working with this gem and cut to the chase without digging through the documentation for themselves. I did my best to keep it newbie-friendly for folks who started to play with tests rather recently. Obviously having been in the same shoes at some point made me believe that it doesn’t take much to make new people feel more comfortable with testing. Taking a bit more time to explain the context and demystifying the lingo goes a long way in cutting down frustration rates for beginners imho.

### Contents

+ Intro & Context
+ Fixtures
+ Configuration
+ Defining factories
+ Using factories
+ Inheritance
+ Multiple records
+ Sequences

+ ### Intro & Context

Let’s start with a little bit of history and talk about the fine folks at [thoughtbot](https://thoughtbot.com/) who are responsible for this popular Ruby gem. Back in 2007/2008 [Joe Ferries](https://github.com/jferris), CTO at thoughtbot, had had it with fixtures and started to cook up his own solution. Going through various files to test a single method was a common pain point while dealing with fixtures. Put differently and ignoring all kinds of inflexiblities, that practice also lead to writing tests that don’t tell you much about their context being tested right away. 

Not being sold on that practice made Joe check out various solutions for factories but none of them supported everything he wanted. So he came up with Factory Girl which made testing with test data more readable, DRY and also more explicit by giving you the context for every test. A couple of years later, [Josh Clayton](https://twitter.com/joshuaclayton), Development Director at @thoughtbot in Boston, took over as the maintainer of the project. Over time this gem has grown steadily and became a “fixture” in the Ruby community. 

Let’s talk more about the main pain point Factory Girl solves. When you build your test suite you’re dealing with a lot of associated records and with information that’s changing frequently. You want to be able to build data sets for your integration tests that are not brittle, easy to manage and explicit. Your data factories should be dynamic and able to refer to other factories—something that is in part beyond YAML fixtures from the old days. Another convenience you want to have is the ability to overwrite attributes for objects on the fly. Factory Girl allows you to do all of that effortlessly—given the fact that it is written in Ruby and a lot of Metaprogramming witchraft is going on behind the scenes—and you are provided with a great domain-specific language. 

Building up your fixture data with this gem can be described as easy, effective and overall more convenient than fiddling with fixtures. That way you can deal more with concepts than with the actual columns in the database. But enough of talking the talk, let’s get our hands a bit dirty.

+ ### Fixtures?

Folks who have experience with testing applications and who don’t need to learn about the concept of fixtures, please feel free to jump right ahead to the next section. This one is for newbies who just need an update about testing data.

Fixtures are sample data—that’s it really! For a good chunk of your test suite you want to be able to populate your test database with data that is tailored to your specific test cases. For quite a while, many devs used YAML for this data—which made it database independent. In hindsight, being independent that way may have been the best thing about it. It was more or less one file per model. That alone might give you an idea about all kinds of headaches people were complaining about. Complexity is a fast growing enemy that YAML is hardly equiped to take on effectively. Below you’ll see what such a **.yml** file with test data looks like.

YAML file: **secret_service.yml**

``` yaml
Quartermaster:
  name: Q
  favorite_gadget: Broom radio
  skills: Inventing gizmos and hacking

00 Agent:
  name: James Bond
  favorite_gadget: Submarine Lotus Esprit
  skills: Getting Bond Girls killed and covert infiltration
```

It looks like a hash, doesn’t it? Its a colon-separeted list of key / value pairs that are separated by a blank space. You can reference other nodes within each other if you want to simulate associations from your models. But I think its fair to say that that’s where the music stops and many say their pain begins. For data sets that are a bit more involved, YAML fixtures are difficult to maintain and hard to change without affecting other tests. I mean you can make them work of course—after all developers used them plenty in the past—but many people agreed that the price to pay for managing fixtures is just a bit stingy. 

To avoid breaking your test data when the inevitable changes occur, developers where happy to adopt newer strategies that offered more flexibility and dynamic behaviour. That’s where Factory Girl came in and left the YAML days behind. Another issue is the heavy dependency between the test and the **.yml** fixture file. [Mystery guests](https://robots.thoughtbot.com/mystery-guest) are also a major pain with these kinds of fixtures. Factory Girl let’s you avoid that by creating objects relevant to the tests inline.

Sure, YAML fixtures are fast and I’ve heard people argue that a slow test suite with Factory Girl data made them go back YAML land. In my mind, if you are using Factory Girl so much that it really slows down your tests, you might be overusing factories unnecessarily and you might ignore strategies that are not hitting the database when you create / save objects. You’ll see what I mean when we get to the relevant section(s). Of course, use whatever you need / see fit but consider yourself warned if you get burned by YAML.

I think it would be fair to add that in the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up test data in your application. They played an important role and helped move the industry forward. Nowadays they have a reasonably bad rep though. Times change so let’s move on to factories which are meant to replace fixtures.

+ ### Configuration

I assume you already have Ruby and RSpec for testing installed on your system. If not, come back after consulting Google and you should be good to go. Its quite straightforward I’d say. You can install the gem manually in your terminal via

**Shell:**
``` bash
gem install factory_girl
```

or add it to your **Gemfile:** 
``` ruby
gem "factory_girl", "~> 4.0"
```
and run `bundle install`.

Now you only need to **require** Factory Girl to complete your setup. Here I’m using RSpec so add the following at the top in **/spec/spec_helper.rb**:

``` ruby
require 'factory_girl'
```

### Ruby on Rails

You are covered of course if you want to use Factory Girl with Rails. The *factory_girl_rails* gem provides a handy Rails integration for *factory_girl*.

**Shell:**

``` bash
gem install factory_girl_rails
```

**Gemfile:**

``` ruby
gem "factory_girl_rails", "~> 4.0"
```

and **require** it of course in **spec/spec_helper.rb**:

``` ruby
require 'factory_girl_rails'
```

### Convenient Syntax Setup

If you prefer typing something like (of course you do)

**Ruby:**

``` ruby
create(:user)
```

instead of 

``` ruby
FactoryGirl.create(:user)
```

everytime you use one of your factories you just need to include the `FactoryGirl::Syntax::Methods` module in your test configuration file. If you forget that step, you have to preface all Factory Girl methods with the same verbose preface. This works with any Ruby app, not just with Rails ones of course. 

For **RSpec** find the **spec/spec_helper.rb** file and add:
``` ruby
require 'factory_girl_rails'

RSpec.configure do |config|
  config.include FactoryGirl::Syntax::Methods
end
```

### Attention!

For the newbies among you, beware that the **RSpec.configure** block will already be there—buried under some amount of comments. You can also do the same setup in a separate file like **spec/support/factory_girl.rb**. In that case you will have to add the whole config block yourself of course. 

The same configuration works if you are using:

+ Test::Unit
+ Cucumber
+ Spinach
+ MiniTest
+ MiniTest::Spec
+ minitest-rails

You can go more fancy with your configuration by throwing in **DatabaseCleaner** for example but the documentation implies that this setup is sufficient to get going so I’ll move on from here.

+ ### Defining Factories

You can define your factories anywhere but they will be loaded automatically if they are placed in the following locations:

**RSpec:**

``` bash
spec/factories.rb
spec/factories/*.rb
```

**Test::Unit:**

``` bash
test/factories.rb
test/factories/*.rb
```

As you can see you have the option to split them into separate files that adhere to whatever logic or bundle your factories together into one big **factories.rb** file. The complexity of your project will be the best guideline for when to logically separate factories into their own separate files.

### Barebones Factories

Factory Girl provides a well-developed ruby DSL syntax for defining factories like *user*, *post* or any other object—not only Active Record objects. “Plain” Ruby classes are perfectly fine. You start by setting up a define block in your **factories.rb** file.

**Ruby:**
``` ruby
FactoryGirl.define do
  #define factories here
end
```

All factories are defined inside this block. Factories just need a **:symbol** name and a set of attributes to get started. The factory below is named **secret_service_agent** and the attributes are **name**, **favorite_gadget** and **skills**.

**Ruby:**
``` ruby
FactoryGirl.define do

  factory :secret_service_agent do
    name "Q"
    favorite_gadget "Submarine Lotus Esprit"
    skills "Inventing gizmos and hacking"
  end

end
```

### Attention!

If you take one thing away from this article then it should be the following: Only define the most barebones factory possible in order to be valid by default—valid in the sense of Active Record validations for example. When Factory Girl calls **save!** on instances your validations will get excercised. If any of them fail, **ActiveRecord::RecordInvalid** gets raised. Defining only the bare minimum gives you more flexibility if your data changes and will reduce the chances of breaking tests and duplication—since coupling is reduced to the core. Don’t be lazy when you compose your factories—it will pay off big time! 

If you think this sounds hard to manage, you’ll most likely be glad to hear that there is a handy solution to segregate objects and their attributes via **Traits**. They are a great strategy to complement your barebones factories and keep them DRY at the same time. My second article will focus quite a bit on this very rad feature of Factory Girl. 

+ ### Using Factories

If you have included the **FactoryGirl::Syntax::Methods** in the configuration step you are able to use the shorthand syntax to create factories in your tests. You have four options which people call *build strategies*:

+ **create**

``` ruby
create(:some_object)
# FactoryGirl.create(:some_object)
```

This one returns an instance of the class the factory emulates. It is recommended to use **create** only when you really need to hit the database. This strategy slows down your test suite if overused unnecessarily. Use it if you want to make use of your validations since it will run **save!** in the background. I think this option is mostly appropriate when you do integration tests where you want to involve a database for your test scenarios.

+ **build**  

``` ruby
build(:some_object)
# FactoryGirl.build(:some_object)
```
It instantiates and assigns attributes but you’ll get an instance returned that is not saved to the database—**build** keeps the object only in memory. If you want to check if an object has certain attributes this will do the job since you don’t need database access for that kinda thing. Behind the scenes it does not use **save!** which means your validations are not run.

### Heads up!

When you use associations with it you might run into a small gotcha. There is a little exception in regards to not saving the object via **build**—it *builds* the object but it *creates* the associations—which I will cover in the section about Factory Girl associations. There is a solution for that in case that’s not what you were shopping for.

+ **build_stubbed** 

``` ruby
build_stubbed(:some_object)
# FactoryGirl.build_stubbed(:some_object)
```

This option was created for speeding up your tests and for test cases where none of the code needs to hit the database. It also instantiates and assigns attributes like **build** does but it only makes objects look like they have been persisted. The object returned has all the defined attributes from your factory stubbed out—plus a fake **id** and **nil** timestamps. Their associations are stubbed out as well—unlike **build** associations which are using **create**. Since this strategy is dealing with stubs and has no dependency on the database these tests will be super fast. 

+ **attributes_for** 

This method will return a hash of only the attributes defined in the relevant factory—without associations, timestamps and id of course. Its sort of convenient if you want to build an instance of an object without fiddling around with attribute hashes manually. I have seen it mostly in Controller specs used similar to this:

**Ruby:**
``` ruby
it 'redirects to some location' do
  post :create, spy: attributes_for(:spy)

  expect(response).to redirect_to(some_location) 
end
```

### Object comparison

To close this section, let me give you one simple example to see the different objects returned from these four build strategies. Below you can compare the four different objects that you’ll get from **attributes_for**, **create**, **build** and **build_stubbed**:



``` ruby
FactoryGirl.define do
  
  factory :spy do
    name "Marty McSpy"
    favorite_gadget "Hoverboard"
    skills "Infiltration and espionage"
  end

end
```

``` ruby
attributes_for(:spy)

#returned object
{:name=>"Marty McSpy", :favorite_gadget=>"Hoverboard", :skills=>"Infiltration and espionage"}
```

``` ruby
create(:spy)

#returned object
<Spy id: 1, name: "Marty McSpy", favorite_gadget: "Hoverboard", skills: "Infiltration and espionage", created_at: "2015-10-17 16:40:09", updated_at: "2015-10-17 16:40:09">
```

``` ruby
build(:spy)

#returned object
<Spy id: nil, name: "Marty McSpy", favorite_gadget: "Hoverboard", skills: "Infiltration and espionage", created_at: nil, updated_at: nil>
```

``` ruby
build_stubbed(:spy)

#returned object
<Spy id: 1001, name: "Marty McSpy", favorite_gadget: "Hoverboard", skills: "Infiltration and espionage", created_at: nil, updated_at: nil>
```

Hope this was helpful if there was still some confusion left how they work.


+ ### Inheritance

You’re gonna love this one! With inheritance you can define factories only with the necessary attributes that each class needs for creation. This parent factory can spawn as many “child” factories as you see fit to cover all kinds of test scenarios with varying data sets. Keeping your test data DRY is very important and inheritance makes this way more easy for you.

Say you wanna define a couple of core attributes on a factory and within that same factory have different factories for the same **Class** with different attributes. In the example below you can see how you can avoid repeating attributes by just nesting your factories.

**factories.rb**

``` ruby
factory :spy do
  name 'Marty McSpy'
  licence_to_kill false
  skills 'Espionage and intelligence'

  factory :quartermaster do
    name 'Q'
    skills 'Inventing gizmos and hacking'
  end
  
  factory :bond do
    name 'James Bond'
    licence_to_kill true
  end
end
```

**some_spec.rb**
``` ruby
bond = create(:Bond)
quartermaster = create(:quartermaster)

bond.skills # => 'Espionage and intelligence'
quartermaster.licence_to_kill # => false
quartermaster.skills # => 'Inventing gizmos and hacking'
```

As you can observe, the **:bond** and **:quartermaster** factories inherit attributes from their parent **:spy**. With that functionality you can easily overwrite attributes for similar objects and be very expressive about it at the same time. Imagine all the lines of code saved because you don’t have to repeat the same basic setup if you want to test different states or related objects. That feature alone is worth it and makes it hard to go back to YAML fixtures.

If you want to avoid nesting factory definitions you can also link factories to their parent explicitly by providing a **parent** hash:

**factories.rb**

``` ruby
factory :spy do
  name 'Marty McSpy'
  licence_to_kill false
  skills 'Espionage and intelligence'
end

factory :bond, parent: :spy do
  name 'James Bond'
  licence_to_kill true
end
```

Same functionality and almost as DRY.

+ ### Multiple Records

Here is a small but nevertheless welcome addtion to Factory Girl that makes dealing with lists easy as pie: 

+ **build_list**
+ **create_list** 

Every once in a while in situations where you want to have multiple instances of some factory “created” without much fuzz, these will come in handy. Both methods will return an array with the amount of factory items specified. Pretty neat right?

**Ruby:**

``` ruby
spy_clones = create_list(:spy, 3)

fake_spies = build_list(:spy, 3)

spy_clones # => [#<Spy id: 1, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">, #<Spy id: 2, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">, #<Spy id: 3, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">]

fake_spies # => [#<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>]
```

Subtle differences, but I’m sure you know the differences by now. I should also mention that you can provide both methods with a hash of attributes if you want to overwrite factory attributes on the fly for some reason. Timewise, the overwrites will eat a bit of your time if you create lots of test data with that strategy.

``` ruby
smug_spies = create_list(:spy, 3, skills: 'Smug jokes')

double_agents = build_list(:spy, 3, name: 'Vesper Lynd', skills: 'Seduction and bookkeeping')

smug_spies # => [#<Spy id: 1, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">, #<Spy id: 2, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">, #<Spy id: 3, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">]

double_agents # => [#<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>] 
```

You’ll frequently need a pair of objects and therefore Factory Girl provides you both of these: 

+ **build_pair** 
+ **create_pair**

Same idea as above but the returned array holds only two records at a time.

``` ruby
create_pair(:spy)
 # => [#<Spy id: 1, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 19:31:41\", updated_at: \"2015-10-18 19:31:41\">, #<Spy id: 2, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 19:31:41\", updated_at: \"2015-10-18 19:31:41\">]

build_pair(:spy)
 # => [#<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>]
```



+ ### Sequences

If you thought naming spies could be more dynamic you are absolutly right. In this final section we’ll look at that exactly.

{% img /images/Factory-Girl-Guide/hine-lewis-national-child-labor-committee-collection.jpg %}

+ ### Associations

{% img /images/Factory-Girl-Guide/Factory_Guide_Association_cropped.png %}

As mentioned before, another trick up the sleeve of Factory Girl is emulating associations—relatively straightforward too I’d say.



##### /spec/factories.rb

``` ruby
FactoryGirl.define do
  
  factory :spy do
    name "Marty McSpy"
    favorite_gadget "Hoverboard"
    skills "Infiltration and espionage"
  end

end
```
##### /spec/spy_spec.rb
``` ruby
require 'rails_helper'
 
describe Spy do
  it 'returns alias of a spy' do
    spy = create(:spy)
    
    expect(spy).to be_instance_of(Spy)
    expect(spy.name).to eq('Marty McSpy')
    expect(spy.function).to eq('Covert agent')
    expect(spy.skills).to eq('Infiltration and espionage')
  end
end

```







``` ruby
FactoryGirl.define do
  
  factory :spy do
    name "Marty McSpy"
    function "Covert agent"
    skills "Infiltration and espionage"
    date_of_birth Date.new(1968, 6, 9) 
  end

  factory :quartermaster, class: Spy do
    name "Q"
    function "Quartermaster"
    skills "Inventing gizmos and hacking"
    date_of_birth nil
  end

  factory :00 agent, class: Spy do
    name "James Bond"
    function "Covert agent"
    skills "Getting Bond Girls killed and covert infiltration"
    date_of_birth nil
  end

end
```



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

When unit-testing most methods, however, Factory Girl (and even persisting data to the database) is unnecessary.

Outro

That’s also one more reason why only defining only the absolute basic objects and their attributes goes a long way in avoiding the side effects of global coupling. -> Bare minimum factory definitions and segregating related objects into separate factories or factories witht traits is the key thing to remember whatever you do. 


A lot of times you actually dont need factory girl to write some of your unit tests. Josh would be the first to attest to that. Bigger systems with lots of data and collaborators and stuff that that you dont need for your unit tests. If you write unit tests and your data doesnt need any associations, it doesnt need persistance, you dont care about callbacks, dont use factory girl or at least use build (stubbed??) which instantiates the record and assignes all the attriubutes, gives it an id and raises if you interact with the database at all.  which gives you fake data that looks like its been persisted - gives you a full-fledged object that looks like its been saved. A lot faster. nothing hits the database at all. 

Global coupling. Data is global in your test suite? it can become painful. When you change some attribute and tests start to break. Idea is to declare factories and then segregate them -> Something is different is named differently. A user with admin flag becomes admin in separate factory or trait. Regular user vs admin user -> Different type of users. Splitting up the data types helps ease some of the pain of the fact that they are global. So like changes to admin are less likely to break global things. 


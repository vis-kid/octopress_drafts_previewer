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

This two-part mini-series was written for people who quickly wanna jump into working with Factory Girl and cut to the chase without digging through the documentation for themselves too much. For folks who started to play with tests rather recently I did my best to keep it newbie-friendly. Obviously having been in the same shoes at some point made me believe that it doesn’t take much to make new people feel more comfortable with testing. Taking a bit more time to explain the context and demystifying the lingo goes a long way in cutting down frustration rates for beginners imho.

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

Not being sold on that current practice made him check out various solutions for factories but none of them supported everything he wanted. So he came up with Factory Girl which made testing with test data more readable, DRY and also more explicit by giving you the context for every test. A couple of years later, [Josh Clayton](https://twitter.com/joshuaclayton), Development Director at @thoughtbot in Boston, took over as the maintainer of the project. Over time this gem has grown steadily and became a “fixture” in the Ruby community. 

Let’s talk more about the main pain points Factory Girl solves. When you build your test suite you’re dealing with a lot of associated records and with information that’s changing frequently. You want to be able to build data sets for your integration tests that are not brittle, easy to manage and explicit. Your data factories should be dynamic and able to refer to other factories—something that is in part beyond YAML fixtures from the old days. Another convenience you want to have is the ability to overwrite attributes for objects on the fly. Factory Girl allows you to do all of that effortlessly—given the fact that it is written in Ruby and a lot of Metaprogramming witchraft is going on behind the scenes—and you are provided with a great domain-specific language that is easy on the eyes too. 

Building up your fixture data with this gem can be described as easy, effective and overall more convenient than fiddling with fixtures. That way you can deal more with concepts than with the actual columns in the database. But enough of talking the talk, let’s get our hands a bit dirty.

+ ### Fixtures?

Folks who have experience with testing applications and who don’t need to learn about the concept of fixtures, please feel free to jump right ahead to the next section. This one is for newbies who just need an update about testing data.

Fixtures are sample data—that’s it really! For a good chunk of your test suite you want to be able to populate your test database with data that is tailored to your specific test cases. For quite a while, many devs used [YAML](https://en.wikipedia.org/wiki/YAML) for this data—which made it database independent. In hindsight, being independent that way may have been the best thing about it. It was more or less one file per model. That alone might give you an idea about all kinds of headaches people were complaining about. Complexity is a fast growing enemy that YAML is hardly equiped to take on effectively. Below you’ll see what such a **.yml** file with test data looks like.

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

Sure, YAML fixtures are fast and I’ve heard people argue that a slow test suite with Factory Girl data made them go back YAML land. In my mind, if you are using Factory Girl so much that it really slows down your tests, you might be overusing factories unnecessarily and you might ignore strategies that are not hitting the database. You’ll see what I mean when we get to the relevant section(s). Of course, use whatever you need / see fit but consider yourself warned if you get burned by YAML.

I think it would be fair to add that in the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up test data in your application. They played an important role and helped move the industry forward. Nowadays they have a reasonably bad rep though. Times change so let’s move on to factories which are meant to replace fixtures.

+ ### Configuration

I assume you already have Ruby and [RSpec](http://rspec.info/) for testing installed on your system. If not, come back after consulting Google and you should be good to go. Its quite straightforward I’d say. You can install the gem manually in your terminal via

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

For the newbies among you, beware that the **RSpec.configure** block will already be there—buried under some amount of comments. You can also do the same setup in a separate file of your own—like **spec/support/factory_girl.rb**. In that case you will have to add the whole config block yourself of course. 

The same configuration works if you are using other libraries for testing:

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

Factory Girl provides a well-developed ruby DSL syntax for defining factories like *user*, *post* or any other object—not only *Active Record* objects. “Plain” Ruby classes are perfectly fine. You start by setting up a define block in your **factories.rb** file.

**Ruby:**
``` ruby
FactoryGirl.define do
  #define factories here
end
```

All factories are defined inside this block. Factories just need a **:symbol** name and a set of attributes to get started. This name needs to be the *snake_cased* version of the Model you want to emulate—like SecretServiceAgent in the following example. The factory below is named **secret_service_agent** and has attributes called **name**, **favorite_gadget** and **skills**.

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

If you think this sounds hard to manage, you’ll most likely be glad to hear that there are handy solutions in place to segregate objects and their attributes. **Inheritance** and **Traits** will become great allies since they are handy strategies to complement your barebones factories and keep them DRY at the same time. Learn more about *Inheritance* below and my second article will focus quite a bit on *Trails*. 

+ ### Using Factories

If you have included the **FactoryGirl::Syntax::Methods** in the configuration step you are able to use the shorthand syntax to create factories in your tests. You have four options which people call *build strategies*:

+ **create**

``` ruby
create(:some_object)
# FactoryGirl.create(:some_object)
```

This one returns an instance of the class the factory emulates. It is recommended to use **create** only when you really need to hit the database. This strategy slows down your test suite if overused unnecessarily. Use it if you want to run your validations since it will run **save!** in the background. I think this option is mostly appropriate when you do integration tests where you want to involve a database for your test scenarios.

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

This option was created for speeding up your tests and for test cases where none of the code needs to hit the database. It also instantiates and assigns attributes like **build** does but it only makes objects look like they have been persisted. The object returned has all the defined attributes from your factory stubbed out—plus a fake **id** and **nil** timestamps. Their associations are stubbed out as well—unlike **build** associations which are using **create** on associated objects. Since this strategy is dealing with stubs and has no dependency on the database these tests will be as fast as they get. 

+ **attributes_for** 

This method will return a hash of only the attributes defined in the relevant factory—without associations, timestamps and id of course. Its sort of convenient if you want to build an instance of an object without fiddling around with attribute hashes manually. I have seen it mostly used in Controller specs similar to this:

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

Hope this was helpful if there was still some confusion left how they work and when to use what option.


+ ### Inheritance

You’re gonna love this one! With inheritance you can define factories only with the necessary attributes that each class needs for creation. This parent factory can spawn as many “child” factories as you see fit to cover all kinds of test scenarios with varying data sets. Keeping your test data DRY is very important and inheritance makes this a lot more easy for you.

Say you wanna define a couple of core attributes on a factory and within that same factory have different factories for the same **Class** with different attributes. In the example below you can see how you can avoid repeating attributes by just nesting your factories. Let’s emulate a **Spy** class that needs to adapt to three different test scenarios.

**factories.rb**

``` ruby
FactoryGirl.define do

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

end
```

**some_spec.rb**
``` ruby
bond = create(:bond)
quartermaster = create(:quartermaster)

quartermaster.name # => 'Q'
quartermaster.skills # => 'Inventing gizmos and hacking'
quartermaster.licence_to_kill # => false

bond.name # => 'James Bond'
bond.skills # => 'Espionage and intelligence'
bond.licence_to_kill # => true
```

As you can observe, the **:bond** and **:quartermaster** factories inherit attributes from their parent **:spy**. At the same time you can easily overwrite attributes as needed and be very expressive about it via their factory names. Imagine all the lines of code saved because you don’t have to repeat the same basic setup if you want to test different states or related objects. That feature alone is worth using Factory Girl and makes it hard to go back to YAML fixtures.

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

Every once in a while in situations where you want to have multiple instances of some factory without much fuzz, these will come in handy. Both methods will return an array with the amount of factory items specified. Pretty neat right?

**Ruby:**

``` ruby
spy_clones = create_list(:spy, 3)

fake_spies = build_list(:spy, 3)

spy_clones # => [#<Spy id: 1, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">, #<Spy id: 2, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">, #<Spy id: 3, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: \"2015-10-18 18:52:02\", updated_at: \"2015-10-18 18:52:02\">]

fake_spies # => [#<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Infiltration and espionage\", created_at: nil, updated_at: nil>]
```

Subtle differences between the two options, but I’m sure you understand them by now. I should also mention that you can provide both methods with a hash of attributes if you want to overwrite factory attributes on the fly for some reason. Timewise, the overwrites will eat a bit of your testing speed if you create lots of test data with that have overwrites. Probably having a separate factory with these attributes changed will be a better option for creating lists.

``` ruby
smug_spies = create_list(:spy, 3, skills: 'Smug jokes')

double_agents = build_list(:spy, 3, name: 'Vesper Lynd', skills: 'Seduction and bookkeeping')

smug_spies # => [#<Spy id: 1, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">, #<Spy id: 2, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">, #<Spy id: 3, name: \"Marty McSpy\", function: \"Covert agent\", skills: \"Smug jokes\", created_at: \"2015-10-18 19:08:07\", updated_at: \"2015-10-18 19:08:07\">]

double_agents # => [#<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>, #<Spy id: nil, name: \"Vesper Lynd\", function: \"Covert agent\", skills: \"Seduction and bookkeeping\", created_at: nil, updated_at: nil>] 
```

You’ll frequently need a pair of objects and therefore Factory Girl provides you another two options: 

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

If you thought naming spies could be less static you are absolutly right. In this final section we’ll look at creating sequential unique values as test data for your factory attributes. When might this be useful? What about unique email addresses for testing authentication or unique usernames for example? That’s where sequences shine. Let’s look at a couple of different ways you can make use of **sequence**:

+ **“global” sequence**

``` ruby
FactoryGirl.define do

  sequence :spy_email do |n|
    "00#{n}@mi6.com"
  end

  factory :spy do
    name 'Marty McSpy'
    email 'marty@thepinheads.com'
    skills 'Espionage and infiltration'
    license_to_kill false

  
    factory :elite_spy do
      name 'Edward Donne'
      license_to_kill true
    end
  end

end

top_spy = create(:elite_spy)
top_spy.email = generate(:spy_email)

top_spy.email
# => "001@mi6.com"
```

Since this sequence is defined “globally” for all your factories—it does not belong to one specifice factory—you can use this sequence generator for all your factories wherever you need a **:spy_email**. **generate** and the name of your sequence is all you need. 

+ **attributes**

As a small variation that is super convenient I’m gonna show you how to directly assign sequences as attributes to your factories. Same condition as above where your sequence is defined “globally”. In the case of a factory definition you can leave off the **generate** method call and Factory Girl will assign the returned value from the sequence directly to the attribute of the same name. Neat!

``` ruby
FactoryGirl.define do

  sequence :email do |n|
    "00#{n}@mi6.com"
  end

  factory :secret_service_agent do
    name 'Edwad Donne'
    email
    skills 'Espionage and infiltration'
    license_to_kill true
  end

end
```

+ **lazy attributes**

You can also use a **sequence** on *lazy attributes*. I will cover this topic in my second article but for completeness sake I wanted to mention it here as well. In case you need a unique value assigned at the time the instance gets created—therefore its called *lazy* because this attribute waits with value assignment until object instantiation—sequences just need a block to work as well.

``` ruby

FactoryGirl.define do

  sequence :mission_deployment do |number|
    "Mission #{number} at #{DateTime.now.to_formatted_s(:short)}"
  end

  factory :spy do
    name 'Marty McSpy'
    deployment { generate(:mission_deployment) }
  end

end

some_spy = create(:spy)
some_spy.deployment # => "Mission #1 at 19 Oct 21:13"
```

The block for the factory attribute gets evaluated when the object gets instantiated. In our case, you’ll get a string composed of a unique mission number and a new **DateTime** object as values for every **:spy** that gets deployed.

+ **in-line sequence**

This option is best when a sequence of unique values is only needed on an attribute for a single factory. It would make no sense to define it outside that factory and possibly have to look for it elsewhere if you need to tweak it. In the example below we are looking to generate unique ids for every spy car.

``` ruby
FactoryGirl.define do 

  factory :aston_martin do
    sequence(:vehicle_id_number) {|n| "A_M_#{n}"}
  end

end

spycar_01 = create(:aston_martin)
spycar_01.vehicle_id_number
# => "A_M_1"

spycar_02 = create(:aston_martin)
spycar_02.vehicle_id_number
# => "A_M_2"
```

Well, maybe we should provide the **vehicle_id_number** attribute another starting value than **1**? Let’s say we wanna account for a couple of prototypes before the car was ready for production. You can provide a second argument as the starting value for your sequence. Let’s go with **9** this time.

``` ruby
FactoryGirl.define do 

  factory :aston_martin do
    sequence(:vehicle_id_number, 9) {|n| "A_M_#{n}"}
  end

end

spycar_01 = create(:aston_martin)
spycar_01.vehicle_id_number
# => "A_M_9"

spycar_02 = create(:aston_martin)
spycar_02.vehicle_id_number
# => "A_M_10"

# ...
```

+ ### Closing Thoughts

As you have seen by now, Factory Girl offers a well balanced Ruby DSL  that builds objects instead of Database Records for your test data. It helps to keep your tests focused, DRY and readable when you deal with dummy data. That’s a pretty solid accomplishment in my book. Remember that barebones factory definitions are key to your future sanity. The more factory data you put in your global test space the more likely you’ll experience some sort of maintainance pain. For your unit tests, Factory Girl will be unnecessary and only slows down your test suite. Josh Clayton would be the first to attest to this and who would recommend its best practice to use Factory Girl selectively and as little as possible.    

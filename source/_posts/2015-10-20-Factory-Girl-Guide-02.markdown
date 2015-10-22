---
layout: post
title: Factory Girl 201
date: 2015-10-20 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

{% img /images/Factory-Girl-Guide/Factory_Guide_Association_cropped.png %}

This second article about this popular and useful Ruby gem deals with a couple more nuanced topics that beginners don’t necessarily need to concern themselves right away when they get started. Again, I did my best to keep it newbie-accessible and explain every bit of lingo people new to TDD might have never heard of.

### Topics

+ Traits
+ Aliases
+ Callbacks
+ Associations
+ Lazy attributes
+ Modifying factories
+ Transient attributes
+ Dependent attributes

+ ### Dependent Attributes

If you need to use attribute values for composing other factory attributes on the fly Factory Girl has you covered. You just need to wrap the whole thing in a block.

``` ruby
FactoryGirl.define do

  factory :supervillain do
    name       'Karl Stromberg'
    passion    'marine biology'
    ambition   'human extinction'
    motivation 'save the oceans'
    profile { "#{name} has a passion for #{passion} and aims to #{motivation} through #{ambition}."}
  end

end

villain = create(:supervillain)
villain.profile 
# => "Karl Stromberg has a passion for marine biology and aims to save the oceans through human extinction."
```

+ ### Transient Attributes

I think its fair to call them fake attributes. These virtual attributes allow you to pass addtional options when you construct your factory intances—via a hash of course. The instance itself won’t be affected by them since they won’t be set on your factory object. On the other hand, Factory Girl treats transient attributes just like real ones. If you use **attributes_for**, they won’t show up though. **Dependent attributes** and **Callbacks** are able to access these fake attributes inside your factory. Overall, they are another strategy to keep your factories DRY. 

``` ruby
FactoryGirl.define do

  factory :supervillain do
    transient do
      megalomaniac false
      cat_owner false
    end

    name       'Karl Stromberg'
    passion    'marine biology'
    ambition   'human extinction'
    motivation { "Building an underwater civilization#{" and saving the world" if megalomaniac}" }
    profile    { "Insane business tycoon#{" – friends with Blofeld" if cat_owner}" }
  end

end

villain = create(:supervillain)
villain.profile
# => "Insane business tycoon"
villain.motivation
# => "Building an underwater civilization"

cat_friendly_villain = create(:supervillain, cat_owner: true)
cat_friendly_villain.profile
# => "Insane business tycoon – friends with Blofeld"

narcissistic_villain = create(:supervillain, megalomaniac: true)
narcissistic_villain.motivation
# => "Building an underwater civilization and saving the world"
```

The example above turns out to be a bit more DRY since I avoided creating separate factories for supervillains that want to save the world or are friends with Blofeld repectively.

!!! Section about callbacks using transient attributes

Transient attributes give you the flexibility to make all kinds of adjustments and avoid creating a host of ever so similar factories.

+ ### Lazy attributes

“Normal” attributes in Factory Girl are evaluated when the factory is defined. You usually provide static values as parameters to methods with the same name of your attributes. If you want to delay the evaluation until the last possible moment—when the instance gets created—you will need to feed attributes their values via a code block. Associations and dynamically created values from objects like **DateTime** objects will be your most frequent customers for that lazy treatment.

``` ruby
FactoryGirl.define do

  factory :exploding_device do
  
    transient do
      countdown_seconds 10*60
      time_of_explosion { Time.now + countdown_seconds }
    end

    activate { "Exploding in #{countdown_seconds} seconds #{time_of_explosion.strftime("at %I:%M %p")}" }
  end

end

ticking_device = create(:exploding_device)
ticking_device.activate
# => "Exploding in 600 seconds at 11:53 PM"
```

To achieve the same expressiveness without **alias** you’d have to get a lot more clever with composing your factories or introduce duplication.






+ ### Associations

As mentioned before, another trick up the sleeve of Factory Girl is emulating associations—relatively straightforward too I’d say.

+ ### Callbacks

Callbacks allow you to inject some code at various moments in the life cycle of an object—like **save**, **after_save**, **before_validation** and so on. Rails for example offers a whole bunch of them and makes it pretty easy for novices to abuse this power. Keep in mind that callbacks that are not related to the persistance of objects are a known anti-pattern and its good advice to not cross that line. For example, it may seem convenient to let an instance of user send emails or process some order in a callback but these kinds of things invite bugs and create ties that are unnecessarily harder to refactor. Maybe that was one of the reasons why Factory Girl “only” offers you five callback options to play with:

+ **before(:create)** executes code block before your factory instance is saved.


+ **after(:create)** executes code block after your factory instance is saved.

+ **after(:build)** executes code block after your factory object has been built in memory

+ **after(:stub)** executes code block after your factory has created a stubbed object 

+ **custom(:your_custom_callback)** executes a custom callback without the need to prepend **before** or **after**.

``` ruby
FactoryGirl.define do
  
  factory :mission do
    target 'Stop the bad dude'
    provided_gadgets 'Mini submarine and shark gun'
    after(:build) { assign_support_analyst }
  end
end
```

#### Attention!

Note that for all options, inside the callback blocks will have an instance of the factory at your disposal. Guarantee you this will come in handy every once in a while, especially with assocations. 

``` ruby
FactoryGirl.define do

  factory :double_agent do
    after(:stub) { |double_agent| assign_new_identity(double_agent) }
  end

end
```
Here a **Ninja** has a bunch of nasty throwing stars (shuriken) at his disposal. Since you have a ninja object you can easily assign the throwing star to belong to the Ninja.

``` ruby
FactoryGirl.define do

  factory :ninja do
    name "Ra’s al Ghul"

    factory :ninja_with_shuriken do

      transient do
        number_of_shuriken 10
      end

      after(:create) do |ninja, evaluator|
        create_list(:shuriken, evaluator.number_of_shuriken, ninja: ninja)
      end

    end
  end

  factory :shuriken do
    name 'Hira-shuriken'
    number_of_spikes 'Four'
    ninja
  end

end

ninja = create(:ninja)
ninja.shuriken # => 0

ninja = create(:ninja_with_shuriken)
ninja.shuriken # => 10

ninja = create(:ninja_with_shuriken, number_of_shuriken: 20)
ninja.shuriken # => 20
```

Also, through the evaluator object you have access to transient attributes too. That gives you the option to pass in addtional information when you “create” factory objects and need to tweak them on the fly. That gives you all the flexibility needed to play with associations and write expressive test data.   


+ ### Aliases

Aliases for your factories allow you to be more expressive about the context that you are using your factory objects in. You only need to provide a hash of alternative names that better describe the relationship between associated objects.

 Let’s say you have an **:agent** factory and a **:law_enforcement_vehicle** factory. Wouldn’t it be nice to refer to the agent as **:owner**? In the example below I contrasted it with an example without an alias.

``` ruby
FactoryGirl.define do

  factory :agent, aliases: [:owner] do
    name   'Fox Mulder'
    job    'Chasing bad dudes'
    special_skills 'Investigation and intelligence'
    
    factory :doubleOseven do
      name 'James Bond'
    end
  end
 
  factory :law_enforcement_vehicle do
    name 'Oldsmobile Achieva'
    kind 'Compact car'
    owner
  end

  factory :spy_car do
    name 'Aston Martin DB9'
    kind 'Sports car'
    doubleOseven
  end

end
```
 I think you’ll agree that it not only reads better but it also gives you or the person who comes after you a bit more context about the objects in question. Yeah, you need to use plural **:aliases** also if you have only a single alias. 

You could write this a bit different as well—a lot more verbose.

``` ruby
factory :agent do
  name   'Fox Mulder'
  job    'Chasing bad dudes'
  special_skills 'Investigation and intelligence'
end  

factory :law_enforcement_vehicle do
  name 'Oldsmobile Achieva'
  kind 'Compact car'
  association :owner, factory: :agent
end
```

Well, not that neat isn’t it?

In the context of comments a **:user** could be referred to as **:commenter**, in the case of a **:crime** a **:user** could be aliased as a **:suspect** and so on. Its no rocket science really—more like convenient syntactic sugar that decreases your need of duplication.


{% img /images/Factory-Girl-Guide/Factory_Guide_Association_cropped.png %}

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


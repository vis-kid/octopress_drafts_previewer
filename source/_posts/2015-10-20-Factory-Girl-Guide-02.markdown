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

This second article about this popular and useful Ruby gem deals with a couple more nuanced topics that beginners don’t necessarily need to concern themselves right away when they get started. Again, I did my best to keep it newbie-accessible and explain every bit of lingo people new to TDD might stumble over.

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

If you need to use attribute values for composing other factory attributes on the fly Factory Girl has you covered. You just need to wrap the whole thing in a block and interpolate the attributes you need. These blocks have access to an *evaluator*—which is yielded to them—and which in turn has access to other attributes, even transient ones. 

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

The example above turns out to be a bit more DRY since there was no need to create separate factories for supervillains that want to save the world or are friends with Blofeld repectively. Transient attributes give you the flexibility to make all kinds of adjustments and avoid creating a host of ever so similar factories.

!!! Section about callbacks using transient attributes


+ ### Lazy attributes

“Normal” attributes in Factory Girl are evaluated when the factory is defined. You usually provide static values as parameters to methods with the same name of your attributes. If you want to delay the evaluation until the last possible moment—when the instance gets created—you will need to feed attributes their values via a code block. Associations and dynamically created values from objects like **DateTime** objects will be your most frequent customers for that lazy treatment.

``` ruby
FactoryGirl.define do

  factory :exploding_device do
  
    transient do
      countdown_seconds 10*60
      time_of_explosion { Time.now + countdown_seconds }
    end

    time_of_explosion { "Exploding in #{countdown_seconds} seconds #{time_of_explosion.strftime("at %I:%M %p")}" }
  end

end

ticking_device = create(:exploding_device)
ticking_device.time_of_explosion
# => "Exploding in 600 seconds at 11:53 PM"
```

+ ### Callbacks

Callbacks allow you to inject some code at various moments in the life cycle of an object—like **save**, **after_save**, **before_validation** and so on. Rails for example offers a whole bunch of them and makes it pretty easy for novices to abuse this power. Keep in mind that callbacks that are not related to the persistance of objects are a known anti-pattern and its good advice to not cross that line. For example, it may seem convenient to use a callback after instantiating something like user to send emails or process some order but these kinds of things invite bugs and create ties that are unnecessarily harder to refactor. Maybe that was one of the reasons why Factory Girl “only” offers you five callback options to play with:

+ **before(:create)** executes a code block before your factory instance is saved. Activated when you use **create(:some_object)**.

+ **after(:create)** executes a code block after your factory instance is saved. Activated when you use **create(:some_object)**.

+ **after(:build)** executes a code block after your factory object has been built in memory. Activated when you use both **build(:some_object)** and **create(:some_object)**.

+ **after(:stub)** executes a code block after your factory has created a stubbed object. Activated when you use **build_stubbed(:some_object)**. 

+ **custom(:your_custom_callback)** executes a custom callback without the need to prepend **before** or **after**.

``` ruby
FactoryGirl.define do
  
  factory :mission do
    objective        'Stopping the bad dude'
    provided_gadgets 'Mini submarine and shark gun'
    after(:build)    { assign_support_analyst }
  end

end
```

#### Attention!

Note that for all callback options, inside the callback blocks you will have access to an instance of the factory via a block parameter. This will come in handy every once in a while, especially with assocations. 

``` ruby
FactoryGirl.define do

  factory :double_agent do
    after(:stub) { |double_agent| assign_new_identity(double_agent) }
  end

end
```
Here a **Ninja** has a bunch of nasty throwing stars (shuriken) at his disposal. Since you have a ninja object in the callback you can easily assign the throwing star to belong to the Ninja.

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

If you find the need to have multiple callbacks on your factory Factory Girl is not standing in your way—even multiple type ones. Naturally, order of execution is top to bottom.

``` ruby
FactoryGirl.define do
  
	factory :henchman do
    name 'Mr. Hinx'
    after(:create) { |henchman| send_on_kill_mission(henchman)  }
    after(:create) { send_cleaner }
	end

end
```

``` ruby
FactoryGirl.define do
  
	factory :bond_girl do
    name 'Lucia Sciarra'
    after(:build)  { |bond_girl| hide_secret_documents(bond_girl)  }
    after(:create) { close_hidden_safe_compartment }
	end

end
```

The devil is in the details of course. If you use **create(:some_object)**, both **after(:build)** and **after(:create)** callbacks will be exectuted.

Multiple build strategies can be bundled to execute the same callback as well.

``` ruby
FactoryGirl.define do
  
	factory :spy do
    name 'Marty McFly'
    after(:stub, :build) { |spy| assign_new_mission(spy) }
	end

end
```

Last but not least, you can even setup something like “global” callbacks that override callbacks for all factories—at least in that particular file if you have separated them into multiple factory files. 

**factories/gun.rb**

``` ruby
FactoryGirl.define do

  before(:stub, :build, :create) { |object| assign_serial_number(object) }

	factory :spy_gun do
    model_name 'Walther PPK'
    ammunition '7.65mm Browning'
    owner

	  factory :golden_gun do
      model_name 'Custom Lazar'
      ammunition '23-carat gold bullet'
	  	after(:create) { |golden_gun| erase_serial_number(golden_gun) } 
	  end
	end

end
```

#### Attention!

If you use inheritance to compose child factories the callbacks on the parent will be inherited as well. 

+ ### Modifying Factories

Probably not a use case you’ll run into everyday but sometimes you inherit factories from other developers and want to change them—in case you are using a TDD’d gem for example. If you feel the need to tweak these legacy factories to better fit your specific test scenarios you can modify them without creating new ones or using inheritance. 

You do this via **FactoryGirl.modify** and it needs to be outside of that particular **FactoryGirl.define** block that you want to change. What you can’t do is modify **sequence** or **trait**—you can override attributes defined in via **trait** though. Callbacks on the “original” factory also won’t be overridden. The callback in your **Factory.modify** block will just be run as next in line. 

``` ruby
FactoryGirl.define do

  factory :spy do
    name 'Marty McSpy'
    skills 'Espionage and infiltration'
    deployment { Time.now }
  end
	
end

FactoryGirl.modify do

  sequence :mission_deployment do |number|
    "Mission #{number} at #{DateTime.now.to_formatted_s(:short)}"
  end

  factory :spy do
    name            'James Bond'
    skills          'CQC and poker'
    favorite_weapon 'Walther PPK'
    body_count      'Classified'
    car             'Aston Martin'
    deployment      { generate(:mission_deployment) }
	end

end
```

In the example above we needed our spies to be a bit more “sophisticated” and use a better mechanism to handle deployment. I have seen examples where gem authors had to deal with time differently than where it was handy to modify factory objects by simply overriding stuff you need to tweak.

### The Last Mile

Let’s bring it all together in the following sections about *associations* and *traits*—yes I also sneaked in **alias** because it was the best place without jumping all over the place. If you have paid attention and remember stuff from the first article everything should fall quite neatly into place now .

+ ### Associations

Associations are essential to every self respecting web app that has a little bit of complexity. A post that belongs to a user, a listing that has many ratings and so forth are the bread and butter developers have for breakfast any day of the week. Seen from that perspective it, becomes obvious that for more complex scenarios factories need to be bullet proof and easy to handle—at least in order to not mess with your TDD mojo. Emulating model associations via FactoryGirl is relatively straightforward I’d say. That in itself is quite amazing in my mind. Achieving a high level of ease and convenience for building complex data sets makes the practice of TDD a no-brainer and so much more effective. 

The new Q has hacker skills and needs to own a decent computer right? In this case you have a **Computer** class and its instances belong to the **Quartermaster** class. Easy right? 

``` ruby
FactoryGirl.define do

  factory :quartermaster do
    name 'Q'
  end

  factory :computer do
    model 'Custom Lenovo ThinkPad W Series'
    quartermaster 
	end

end
```

What about something a bit more involved? Let’s say our spies use a **gun** that has_many **cartridge(s)** (bullets).

``` ruby
class Cartridge < ActiveRecord::Base 
  belongs_to :gun 
end

class Gun < ActiveRecord::Base
  has_many :cartridges
end
```

``` ruby
FactoryGirl.define do
	factory :cartridge do
    caliber '7.65' 
    gun
  end

	factory :gun do
    model_name 'Walther PPK'
    ammunition '7.65mm Browning'
    caliber    '7.65'

    factory :gun_with_ammo do
      transient do
        magazine_size 10
      end

      after(:create) do |gun, evaluator|
        create_list(:cartridge, evaluator.magazine_size, gun: gun)
      end
    end
  end
end
```

Callbacks come in pretty handy with associtations huh? Now you can build a gun with or without ammunition. Via the hash **gun: gun** you provided the **cartridge** factory with the necessary information to create the association.

``` ruby
spy_gun = create(:gun)
spy_gun.cartridges.length # => 0

spy_gun_with_ammo = create(:gun_with_ammo)
spy_gun_with_ammo.cartridges.length # => 10
```

If you need another magazine size you can pass it in via your **transient attribute**.

``` ruby
big_magazine_gun = create(:gun_with_ammo, magazine_size: 20)
big_magazine_gun.cartridges.length # => 20
```

So what about the different build strategies? Wasn’t there something fishy? Well, here’s what you need to remember: If you use **create** for associated objects both of them will be saved. So **create(:quartermaster)** will build and save both Q and his Thinkpad. Then I better use **build** then if I want to avoid hitting the database? Good idea but **build** would only apply to **quartermaster** in our example—the **computer** would still get saved. A bit tricky I know. Here’s what you can do if you need to avoid saving the associated object—you specify the build strategy you need for your association.

``` ruby
FactoryGirl.define do

  factory :quartermaster do
    name 'Q'
  end

  factory :computer do
    model 'Custom Lenovo ThinkPad W Series'
    association :quartermaster, strategy: :build
	end

end

```

You name the associated factory object and pass in a hash with your build strategy. You need to use the explicit **association** call for this to work. The example below won’t work.

``` ruby
factory :computer do
  model 'Custom Lenovo ThinkPad W Series'
  quartermaster, strategy: :build
end
```

Now both objects use **build** and nothing gets saved to the database. We can check that assumption by using **new_record?** which returns **true** if the instance hasn’t been persisted.

``` ruby
q = build(:quartermaster)
q.new_record? # => true
q.computer.new_record? # => true
```

While we’re at it, via the explicit **association** call you can also refer to different factories and change attributes on the fly.

``` ruby
FactoryGirl.define do

  factory :quartermaster do
    name 'Q'
  end

  factory :computer do
    model 'Custom Lenovo ThinkPad W Series'
    association :hacker, factory: :quartermaster, skills: 'Hacking'
	end

end
```

Let’s close this chapter with an example that is **polymorphic**. 

``` ruby
class Spy < ActiveRecord::Base
  belongs_to :spyable, polymorpic: true
end

class MIFive < ActiveRecord::Base
  has_many :spies, as: :spyable
end

class MISix < ActiveRecord::Base
  has_many :spies, as: :spyable
end
```

``` ruby
FactoryGirl.define do

  factory :mifive do
    name 'Military Intelligence, Section 5'
    principal_activity 'Domestic counter-intelligence'
  end

  factory :misix do
    name 'Military Intelligence, Section 6'
    principal_activity 'Foreign counter-intelligence'

  end

  factory :mifive_spy, class: Spy do
    association :spyable, factory: :mifive 
  end

  factory :misix_spy, class: Spy do
    association :spyable, factory: :misix
  end

end
```

Don’t feel bad if this one needs a bit more time to sink in. I recommend catching up on [**Polymorphic Associations**](http://guides.rubyonrails.org/association_basics.html#polymorphic-associations) if you’re unsure what’s going on here.

+ ### Aliases

Aliases for your factories allow you to be more expressive about the context that you are using your factory objects in. You only need to provide a hash of alternative names that better describe the relationship between associated objects.

 Let’s say you have an **:agent** factory and a **:law_enforcement_vehicle** factory. Wouldn’t it be nice to refer to the agent as **:owner**? In the example below I contrasted it with an example without an alias.

``` ruby
FactoryGirl.define do

  factory :agent, aliases: [:owner] do
    name   'Fox Mulder'
    job    'Chasing bad dudes'
    special_skills 'Investigation and intelligence'
    
    factory :double_O_seven do
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
    double_O_seven
  end

end
```
 I think you’ll agree that using aliases it not only reads better but it also gives you or the person who comes after you a bit more context about the objects in question. Yeah, you need to use plural **:aliases** also if you have only a single alias. 

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

In the context of comments a **:user** could be referred to as **:commenter**, in the case of a **:crime** a **:user** could be aliased as a **:suspect** and so on. Its no rocket science really—more like convenient syntactic sugar that decreases your temptation for duplication.


+ ### Traits

This is one of my favorite things about Factory Girl. In a nutshell, traits are lego-like blocks to build your factories and mix in behaviour. In my mind, **trait** is the most powerful and convenient function to keep your factory data DRY and expressive. It allows you to bundle groups of attributes together, give them separate names and reuse them wherever you please. Remember when I urged you to define barebones factory objects? Traits will help you achieve exactly that without sacrificing any conveniences.

``` ruby
FactoryGirl.define do
    
  factory :spy_car do
    model         'Aston Martin DB9'
    top_speed     '295 km/h'
    build_date    '2015'
    ejection_seat true

    trait :submarine do
      ejection_seat              false
      water_resistant            '100 m'
      submarine_capabilities     true
      air_independent_propulsion true
    end
    
    trait :weaponized do
      rockets           true
      number_of_rockets '12'
      machine_gun       true
      rate_of_fire      '1,500 RPM'
      tank_armour       true
    end
    
    trait :cloaked do
      active_camouflage true
      radar_signatur    'reduced'
      engine            'silenced'
    end
    
    trait :night_vision do
      infrared_sensors true
      heads_up_display true
    end
  end

end
```

As you can see, if you want to change some attributes that are now spread over multiple objects, you can do that now in one central place. No shotgun surgery necessary. Managing state via traits couldn’t be more convenient. With that setup you can build pretty elaborate spy cars by mixing the various attribute bundles however you like—without duplicating anything via creating all sorts of new factories that account for all the various options you need. 

``` ruby
invisible_spy_car = create(:spy_car, :cloaked, :night_vision)
diving_spy_car    = create(:spy_car, :submarine, :cloaked)
tank_spy_car      = create(:spy_car, :weaponized, :night_vision)
```
You can use traits with **create**, **build**, **build_stubbed** and **attributes_for**. If Q gets clever, you can also ovverride individual attributes simultaneously by passing in a hash.

``` ruby
build(:spy_car, :submarine, ejection_seat: true)
```

For trait combinations that occur very frequently on a particular factory you can also create child factories which names best represent that data set. That way you bundle their traits only once as opposed to all the time when you create test data.

``` ruby
FactoryGir.define do

  factory :spy_car do
    model         'Aston Martin DB9'
    top_speed     '295 km/h'
    build_date    '2015'
    ejection_seat true

    trait :submarine do
      ...
    end
    
    trait :weaponized do
      ...
    end
    
    trait :cloaked do
      ...
    end
    
    trait :night_vision do
      ...
    end
  end

    factory :invisible_spy_car, traits: [:cloaked, :night_vision]
    factory :diving_spy_car,    traits: [:submarine, :cloaked]
    factory :tank_spy_car,      traits: [:weaponized, :night_vision]
    factory :ultimate_spy_car,  traits: [:cloaked, :night_vision, :submarine, :weaponized]

end
```

That let’s you create these objects more much concise—more readable too.

``` ruby
build_stubbed(:invisible_spy_car)
create(:ultimate_spy_car)
```

Instead of:

``` ruby
build_stubbed(:spy_car, :cloaked, :night_vision)
create(:spy_car, :cloaked, :night_vision, :submarine, :weaponized)
```

Reads much better no? Especially when no variable names are involved.

You can even reuse traits as attributes on other traits and factories. If you define the same attributes for multiple traits, the last one defined gets precedence of course. 


``` ruby
FactoryGirl.define do

  factory :spy_car do
    model         'Aston Martin DB9'
    top_speed     '295 km/h'
    build_date    '2015'
    ejection_seat true

    trait :submarine do
      ...
    end
    
    trait :weaponized do
      ...
    end
    
    trait :cloaked do
      ...
    end
    
    trait :night_vision do
      ...
    end

    trait :mobile_surveillance do
      cloaked
      night_vision
      signal_detector      true
      signal_analyzer      true
      wifi_war_driver      true
      license_plate_reader true
      mini_drone           true
    end
  end

  factory :ultimate_spy_car, parent: :spy_car do
    car_plane true
    submarine
    cloaked
    night_vision
    weaponized
    mobile_surveillance
  end

end

```

Pay attention to the **mobile_surveillance** trait which reuses the **cloaked** and **night_vision** traits—basically as an attribute. Also the **ultimate_spy_car** factory, which I separated out of the **spy_car** factory definition for fun this time, reuses all traits plus an additional attribute that makes it fly too. Movie magic or maybe I should say FactoryGirl magic.

**create_list** and **build_list** can also make use of traits. The second parameter needs to be the number of factory instances you want.

``` ruby
create_list(:spy_car, 3, :night_vision)
build_list(:spy_car, 4, :submarine, :cloaked)
```

Wouldn’t it be cool to use associations with traits? Of course you can pack callbacks and associations neatly into traits. Duh!

``` ruby
FactoryGirl.define do
	factory :cartridge do
    caliber '7.65' 
    gun
  end

	factory :gun do
    model_name 'Walther PPK'
    ammunition '7.65mm Browning'
    caliber    '7.65'

    trait :with_ammo do
      transient do
        magazine_size 10
      end

      after(:create) do |gun, evaluator|
        create_list(:cartridge, evaluator.magazine_size, gun: gun)
      end
    end
  end
end
```

How to use them should be boring by now.

``` ruby
gun_with_ammo = create(:gun, :with_ammo)
gun_with_ammo.cartridges.length # => 10
```

Last words of wisdom: Change is your constant companion—needing to change attributes or data types happens all the time. Design decisions like these evolve. Traits will ease the pain with that and helps you manage your data sets. Imagine if you’d have used an options hash for instantiation and that requirement totally changed. How many potential places in your tests might break and will now need attention? Straight up, **trait** is a very effective tool for eliminating duplication in your test suite. But with all that convenience, don’t be lazy and forget your unit tests on the columns that are represented by your traits! That way you give them the same amount of care as the barebones attributes needed for valid objects.

+ ### Final Thoughts

There is a bit more to discover in FactoryGirl and I’m confident you are now more than well equipped to to put the pieces together when you need them. Have fun playing with this gem. I hope your TDD habits will benefit from it. 

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

override on the fly

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


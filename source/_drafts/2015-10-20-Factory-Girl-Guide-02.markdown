---
layout: post
title: Factory Girl 201
date: 2015-10-20 04:29:10 +0100
comments: true
sharing: true
published: false 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

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


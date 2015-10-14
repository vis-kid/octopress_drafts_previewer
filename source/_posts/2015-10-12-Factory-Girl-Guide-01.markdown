---
layout: post
title: Factory Girl Survival Guide 01
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
+ Configuration
+ Fixtures
+ Defining fixtures
+ Using fixtures
+ Associations
+ Inheritance
+ Multiple records
+ Sequences

+ ### Intro & Context

Let’s start with a little bit of history and talk about the fine folks at [thoughtbot](https://thoughtbot.com/) who are in charge of this popular Ruby gem. Back in 2007/2008 [Joe Ferries](https://github.com/jferris), CTO at thoughtbot, had had it with Fixtures and started to cook up his own solution. Going through various files to test a single method was a common pain point while dealing with fixtures. Put differently, that practice also lead to writing tests that don’t tell you much about their context being tested right away. 

Not being sold on that practice made him checkout various solutions for factories but none of them supported everything he wanted. After his first attempt, Factory Girl made tests more readable, DRY and also more explicit by giving you the context for every test. A couple of years later, [Josh Clayton](https://twitter.com/joshuaclayton), Development Director at @thoughtbot in Boston, took over as the maintainer for the project. Since then the project has grown steadily and has become a go-to solution in the Ruby community 

What’s the main pain point that’s being solved today? When you build your test suite you’re dealing with a lot of associated records and with information that’s changing frequently. You further want to be able to build data sets for your integration tests that are not brittle, easy to manage and explicit. Your data factories should be dynamic and able to refer to other factories—something that is reasonably far beyond YAML fixtures from the old days. Another convenience you want to have is the ability to overwrite attributes for objects on the fly.

Factory Girl allows you to do all of that effortlessly—given the fact that a lot of Metaprogramming witchraft is going on behind the scenes which provides you with a great domain-specific language. Building up your fixture data with this gem can be described as easy, effective and overall more convenient. That way you can deal more with concepts than with the actual columns in the database. But enough of talking the talk, let’s get our hands a bit dirty.



Notes:
Factory girl is a ruby gem

goal is to replace rails fixtures

fixtures were the de facto standard for setting up data for testing your application

it was data stored in yaml

and it was difficult to maintain
hard to change without affecting other tests

provides ruby dsl syntax for defining factories like user, post, any object, not only active record objects

have post with cretain attributes
can have associations(references to other factories), callbacks(for you create data), traits(mix in behaviour)

it written in ruby instead of yaml like fixtures

references to related data

overwrite on the fly

josh maintainer of factory girl

attribute order does not matter

uses a lot of metaprogramming with a lot of dynamic method definitions

you can refer to attributes on objects even if you haven’t defined them in your factory. that means you can define factories with the absolute bare minimum to make them valid objects and assign attributes needed for your tests on the fly.


Want to have it DRY

Outro
Global coupling. Data is global in your test suite? it can become painful. When you change some attribute and tests start to break. Idea is to declare factories and then segregate them -> Something is different is named differently. A user with admin flag becomes admin in separate factory or trait. Regular user vs admin user -> Different type of users. Splitting up the data types helps ease some of the pain of the fact that they are global. So like changes to admin are less likely to break global things. 

That’s also one more reason why only defining only the absolute basic objects and their attributes goes a long way in avoiding the side effects of global coupling. -> Bare minimum factory definitions and segregating related objects into separate factories or factories witht traits is the key thing to remember whatever you do. 

Declaring the most barebones factory as possible in order for it to be valid by default. In terms of Active Record validations. Facotry Girls will call save! on instance when its being created. which means that validations are always run. If any of them fail ActiveRecord::RecordInvalid gets raised. 

create is actually going to persist the data. the goal is declaring bare bones factories and either have child factories and assign different attribues that mutate the data in a certain manner or that you use traits where you can mutate multiple columns (attributes) and change a handful of columns at at time or you can add associations or different callbacks. 

Being able to define traits as a concept. for example flag on a post. published boolean and then requirements change and you need timestamp. later it changes again because you need state machine with published, not published, draft and so one. Instead of building a factory girl object and putting that column when you create this record ends up being a painfpoint because everything needs to change now when you refer to published-> all through your tests. -> traits just referring to this concept which lets you apply and affect changes in only one central DRY place. Imagine if you’d have used an options hash for instantiation and that requirement totally changed how many potential places in your tests might break and need attention now. with traits your test dont necessarily care about how its stored in the database. 

traits are comma separated list of symbol traits / attributes that you wanna addon to a particular factory. traits are also defined in your factories file(s). traits is a tool for eliminating duplication in your test suite. 

And because you still do (hopefully) unit tests on the columns  for your models that are represented in traits you give them you still give them the same amount of care than the attributes that are needed to have valid objects. 

Facotry girl is a pretty popular project in the Ruby community

The more date you put in your global test space the more pain you are gonna have maintainance wise. Don’t be lazy!

A lot of times you actually dont need factory girl to write some of your unit tests. Josh would be the first to attest to that. Bigger systems with lots of data and collaborators and stuff that that you dont need for your unit tests. If you write unit tests and your data doesnt need any associations, it doesnt need persistance, you dont care about callbacks, dont use factory girl or at least use build (stubbed??) which instantiates the record and assignes all the attriubutes, gives it an id and raises if you interact with the database at all.  which gives you fake data that looks like its been persisted - gives you a full-fledged object that looks like its been saved. A lot faster. nothing hits the database at all. 



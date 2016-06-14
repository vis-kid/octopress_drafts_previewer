---
layout: post
title: RSpec Testing for Beginners
date: 2016-05-18 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, TDD, BDD, Testing, Test-Driven-Development Ruby, Ruby on Rails]
---

{% img /images/RSpec/RSpec-Testing-Red-with-Logo.jpg %}

## Topics

+ What’s The Point?
+ RSpec?
+ Getting Started
+ Running Tests
+ Basic Syntax
+ Four Phases Of Tests
+ The Hard Think About Testing

## What’s The Point?

What is RSpec good for? RSpec is very useful on the Unit Test level, testing the finer details and the business logic of your app. That means testing the internals like models and controllers of your app. Tests that cover your views or feature tests which simulate more complete user flows like purchasing an item, will not be the focus that RSpec is made for. RSpec does not make use of a web driver—like Capybara does for example—that simulates a users interactions with an actual site or a representation of it.

Test-driven development (TDD), what’s the point? Well, that is not that easy to answer without feeding you some clichés. I hope this doesn’t sound evasive. I could provide a quick answer but I want to avoid sending you home hungry after having only a small snack. The result of this little series about RSpec and testing should not only give you all the info to answer this question yourself but also provide you with the means and the understanding to get started with testing while feeling a bit confident already about the testing thing.

Beginners seem to have a harder time getting into RSpec and the TDD workflow than starting to get dangerous with Ruby or Rails. Why is that? I can only guess at this point but on the one hand, the literature seems mostly focused on people who already have some programming skills under their belt and on the other hand, learning all the stuff that is involved to have a clear understanding is a bit daunting. The learning curve can be quite steep I suppose. For effective testing, there are a lot of moving parts involved. It’s a lot to ask for newbies who have just started to understand a framework like Rails to look at the process of building an app from the opposite perspective and learn a complete new API to write code for your code.

I thought about how to approach this “dilemma” for the next generation of coders who are just looking for a smoother start into this whole thing. This is what I came up with. I will break the most essential syntax down for you without assuming much more than basic knowledge of Ruby and a little bit of Rails. Instead of covering every angle possible and confuse you to death, we will go over your basic survival kit and try to paint the bigger picture. We will discuss the “How?” rather verbosely to not loose new coders along the way. The second part of the equation will be explaining the “Why?”. If I’m lucky, you’ll get away with a good basis for more advanced books while feeling confident about the bigger picture? Ok now, let’s walk the walk! 

### Benefits And Such

Let’s get back at the purpose of testing. Is testing useful for writing better quality apps? Well, this can be hotly debated, but at the moment I’d answer this questions with a yes—I’m in the hipster TDD camp I guess. Let’s see why tests provide your apps with a couple of benefits that are hard to ignore:

+ They check if your work functions as intended. Constantly validating that you are writing code that works is essential to the health of your application and your team’s sanity.

+ They test stuff you don’t want to test by hand, tedious checks that you could do by hand—especially when you’d need to check this all the time. You want to be as sure as possible that your new function or your new class or whatever does not cause any side effects in maybe completely unforeseen areas of your app. Automating that sort of a thing saves you not only a TON of time but will also make testing scenarios consistent and reproduceable. That alone makes them much more dependable than error-prone testing by hand.

+ We want to make sure the apps behaves in a certain way, in an expected way. Tests can make sure to a pretty high degree that the way users interact with your app is functioning and avoiding bug scenarios that you were able to foresee. Tests check that your application works they way you designed it—and that it continues to work after you introduced modifications. This is especially important when your test suite informs you about failing scenarios about implementations of your app that might be old and therefore not exactly at the back of your brain anymore and not considered while you introduced some new functionality. In short, it helps to keep your app healthy and avoids introducing tons of bugs.

+ Automating tests make you actually test more frequently. Imagine if you have to test something for the 40th time for some reason. If it is only a bit time consuming, how easy will it be to get bored and skip the process altogether. These sort of things are the first step on a slippery slope where you can kiss a decent percentage of code coverage goodbye.

+ Tests work as documentation. Huh? The specs you write give other people on your teams a quick entry point to learn a new code base and understand what it is supposed to do. Writing your code in RSpec for example is very expressive and forms highly readable blocks of code that tell a story if done right. Because it can be written very descriptively while also being a very concise Domain Specific Language (DSL), RSpec hits two birds with one stone. Not being verbose in it’s API and providing you with all the means to write highly understandable test scenarios. That’s what I always liked about it and why I never got really warm with [Cucumber](https://cucumber.io/) which was solving the same issue overly client-friendly I think.

+ They can minimize the amount of code you write. Instead of spiking around like crazy, trying out stuff more freestyle, the practice of test-driving your code let’s you write only the code that is necessary to pass your tests. No excess code. A thing you will often hear in your future career is that the best code is code you don’t have to write or something. Why? Well, most often, more elegant solutions involve lesser amounts of code and also, code that you don’t write—which might be unnecessary btw—won’t cause any future bugs or don’t need to be maintained. So writing the tests first, before you write the implementation, gives you a clear focus which problem you need to solve next. Writing only code that is necessary, and not accidently more, is maybe an underestimated side effect that TDD can provide you with. 

+ They have a positive effect on your design. To me, understanding this part turned on a light bulb and made me really appreciate the whole testing thing. When you write your implementations around very focused test scenarios, your code will most likely turn out to be much more compartmentalized and modular. Since we are all friends of DRY—“Don’t repeat yourself!”—and as little coupling between components in your app as possible, this a simple but effective discipline to achieve systems which are designed well from the ground up. This aspect is the most important benefit I think. Yes, the other ones are pretty awesome as well, but when tests also result in apps which quality is better due to a refined design, I’d say Jackpot! 

+ It boils down to money as well. When you have a stable app that is easy to maintain and easy to change you will save quite a bit of money in the long run. Unnecessary complexity can haunt projects easily and motivation won’t be on an all-time high when your team has to fight your code because it’s brittle and badly designed. Good application design can absolutley support your business goals—and vice versa. Wanna introcude some new features that are critical to your business but you are constantly fighting your architecture because it was built on sand? Of course not, and we all have seen lots of examples of businesses who quickly disappeared for that exact reason. Good testing habits can be an effective line of defense for such situations. 

+ Another goal that is important is in respect to the quality of your code itself. The software you write should be easy to understand for other developers—as much as possible at least. Your tests can really help to convey the functionality and intent of your application—and not only to other members on a team but to your future self as well. If you don’t touch a certain section of your code for quite a while, it will really be handy to refresh your memory of how and why you wrote a piece of software with the documentation that a tool such as RSpec provides—and RSpec does this really well, exceptionally actually.

+ Since your code will always change, refactoring your code will and should always be part of developing your software. And since change is so baked into this process, you need to make sure that these changes don’t generate unexpected side effects in surprising places. The test suite gives you a pretty tight-knit security net to feel more comfortable and free to refactor with gusto. This aspect, next to the design benefits TDD can provide you with, is my favorite benefit a test suite can help you with. Modifying and extending your code is such an essential component of innovating on your already released “product” that you need a tool that gives you as much freedom as possible with that process. I’m not sure if people who are critical of writing an extensive test suite are much concerned about this aspect.

+ You will have a good chance of building new stuff faster faster in later stages because the feedback from the test suite will give you feedback about your failures, bugs and limitations. Much faster than a human can test of course. Pluse, it will give you the confidence of working with a security net that becomes even more valuable the longer you go. In your apps, especially if they have grown significantly, you want to be able to trust your software. 100% code coverage sounds a lot sweeter when you have a site that is a couple of years old and touched by hundreds of developers. Being able to trust the new code you introduce and build on top of that is one of the blisses of software development that money can’t buy later on.

## Getting Started

``` ruby

rails new your_app -T

```

`-T` let’s you skip Test Unit, the testing framework that comes with Rails

###### Gemfile

``` ruby

group :development, :test do

gem 'rspec-rails'

end

```

###### Terminal

``` bash

bundle

```

After that we need to run a generator that comes with RSpec:

###### Terminal

``` bash

rails generate rspec:install

```

###### Output

``` bash

create  .rspec
create  spec
create  spec/spec_helper.rb
create  spec/rails_helper.rb

```

What this does is setting up the basic structure for your RSpec tests within rails. As you can see from the output above, this generator initialized a `spec` directory with a few files that you will need later. The `.rspec` file is a configuration file that we won’t need to manipulate for now. Just wanted to let you know what you have in front of you. The other files are self-explanatory but I want to mention their differences. `spec_helper.rb` is for specs that don’t depend on Rails, `rails_helper.rb` on the other hand do depend on it. What is not obvious is that one of these files need to be required on top of your spec files (test files) in order to run your tests. Let’s have a quick look! When you generate a model via: 

###### Terminal

``` bash 

rails generate model dummy_model name:string

```

###### Output

``` bash

invoke  active_record
create    db/migrate/20160521004127_create_dummy_models.rb
create    app/models/dummy_model.rb
invoke    rspec
create    spec/models/dummy_model_spec.rb

```

Not only will Rails have created the associated `_spec.rb` files for you, your specs will also automatically have ```require 'rails_helper'``` by default on top of your spec files. That means you are ready to go, right away. 

###### spec/models/dummy_model_spec.rb

``` ruby

require 'rails_helper'

...

```

So with this setup, you can test your Rails app, your models for example, and RSpec will not get confused about model classes used in Rails. This is necessary to require whenever you need stuff like `ActiveRecord`, `ApplicationController` and so on. So this is your normal scenario and therefore should be your fist logical choice as a beginner.

Requiring ```spec_helper.rb``` on the other hand, will throw an error if you write tests that include business logic from your Rails app. In that scenario, RSpec would not know what you are talking about when you want to test some Rails model for example. So long story super short, ```spec_helper``` does not load Rails—that’s it! Of course you can go wild with configurations, but this is nothing I want you to be concerned about right now. Let’s focus on the basics, how to run tests and the syntax. The should suffice for starters. Let’s move on!

## Running Tests

You are ready to run your tests. RSpec requires your test files to have a specific suffix like ```_spec``` to understand which files to run. If you use a generator, this is not a concern actually but if you want to write your test files on your own, this is how they need to end. So you will need to put a file like ```your_first_test_spec.rb``` in your `spec` directory. Using the generator for creating a dummy model already provided us with ```spec/models/dummy_model_spec.rb```. Not bad! One thing left to do before the tests are ready: 

###### Terminal

```

rake db:migrate
rake db:test:prepare

```

These commands ran your migration for the dummy model we generated above—plus sets up the test database with that model as well. Now we actually run the test:

###### Terminal

``` bash

rake

```

The `rake` command will run all your tests, the complete test suite. Usually you should use this command when you have finished some feature and want to exercise the whole test suite.

###### Output

``` bash

*

Pending: (Failures listed here are expected and do not affect your suite's status)

1) DummyModel add some examples to (or delete) /Users/vis_kid/projects/rspec-test-app/rspec-dummy/spec/models/dummy_model_spec.rb
   # Not yet implemented
   # ./spec/models/dummy_model_spec.rb:4

Finished in 0.00083 seconds (files took 1.94 seconds to load)
1 example, 0 failures, 1 pending

```

Congratulations! You just ran your first RSpec test. Not that bad, was it? Of course this was a dummy test for now—with dummy test code generated by Rails. The more focused version of running your tests—you have actually a lot more options than only that—is to run an individual file for example. Like this for example:

###### Terminal

``` bash

bundle exec rspec spec/models/dummy_model_spec.rb

```

This will only run a single test file instead of the whole test suite. With larger applications that depend on a large amount of test files, this will become a real time saver. But in terms of saving time and test specificity, this is only scratching the surface to be frank. I think we will cover more of how to shave off significant amount of times while testing in the third article in this series. Let’s see how far we get!

The other way to exercise the whole test suite is by simply running `rspec`—with or without `bundle exec`, depending on your setup.
###### Terminal

``` bash

bundle exec rspec

```

One more thing I should mention before we move on, you can also run only a specific subset of tests. Say you only want to run all of your tests for your model code:

###### Terminal

``` bash

bundle exec rspec spec/models

```

Easy as that!

## Basic Syntax

I recommend that we start with the bare basics and look into a few more options that RSpec provides in the next two articles. Let’s have a look at the basic structure of a test and dive into more advanced waters when we got this one out of the way.

+ `describe`

This will be your bread and butter because it organizes your specs. Within you can reference strings or classes themselves:

###### Some Spec

``` ruby

describe User do

end

describe 'Some string' do

end

```

`describe` sections are the basic building blocks to organize your tests into logical, coherent groups to test. Basically a scope for different parts of your application that you want to test.

###### Some Spec

``` ruby


describe User do
  ...
end

describe Guest do
  ...
end

describe Attacker do
  ...
end

```

A good tip is to tighten your scope even more. Since some classes will grow quite significantly, it’s not a good idea to have all the methods you want to test for one class in a single `describe` block. You can create multiple of these blocks of course and focus them around instance or class methods instead. To make your intent more clear, all you need is to provide the class name with an extra string that references the method you want to test.

###### Some Spec

``` ruby

describe Agent, '#favorite_gadget' do
  ...
end

describe Agent, '#favorite_gun' do
  ...
end

describe Agent, '.gambler' do
  ...
end

```

That way you get the best of both worlds. You encapsulate related tests in their representative groups while keeping things focused and at a decent size. For users very new to Ruby land, I should mention that that `#` simply refernces an instance method while the dot `.` is reserved for class methods. Because they are inside of strings, they have no technical implications here but they signal your intent to other developers and your future self. Don’t forget the comma after the class name, won’t work without it! In a minute, when you get to `expect` I’ll show you why this approach is super convenient.

+ `it`

Within the scope of `describe` groups, we use another scope of `it` blocks. These are made for the actual examples under test. If you want to test the instance method `#favorite_gadget` on the `Agent` class, it would look like this:

###### Some Spec

``` ruby

describe Agent, '#favorite_gadget' do

  it 'returns one item, the favorite gadget of the agent ' do
    ...
  end

end

```

The string that you provide to the `it` block works as the main documentation for your test. Within it, you specify exactly what kind of behaviour you want or expect from the method in question. My recommendation is to not go overboard and be too verbose about it but at the same time to not be overly cryptic and confuse others with overly smart descriptions. Think about what the smallest and simplest implementation of this part of the puzzle can and should accomplish. The better you write this part, the better the overall documentation for your app will end up. Don’t rush this part just because it’s a string that can’t do any damage—at least not on the surface.

+ `expect()`

Now we’re getting more to the heart of things. This method let’s you verify the part of the system you want to test. Let’s go back to our previous example and see it in (limited) action:

###### Some Spec

``` ruby

describe Agent, '#favorite_gadget' do

  it 'returns one item, the favorite gadget of the agent ' do
    expect(agent.favorite_gadget).to eq 'Walther PPK' 
  end

end

```

`expect()` is the “new” assertion syntax of RSpec. Previously we used `should` instead. Different story, but I wanted to mention it in case you run into it. `expect()` expects that you provide it with the object under test and exercise whatever method under test on it. Finally, you write the asserted outcome on the right side. You have the option to go the positive or negative route with `.to eq` or `.not_to eq` for example. Up to you, you can always turn the logic around, every way it suits best your needs. Let’s run this nonsensical test and focus on the output that we got as a result of our test setup:

###### Terminal

``` bash

rspec spec/models/agent_spec.rb

``` 

###### Output

``` bash

Failures:

  1) Agent#favorite_gadget returns one item, the favorite gadget of the agent 
     Failure/Error: expect(agent.favorite_gadget).to eq 'Walther PPK'

```

Reads pretty nice, doesn’t it? **"Agent#favorite_gadget returns one item, the favorite gadget of the agent"** tells you all you need to know:

+ The class involved
+ The method under test
+ The expected outcome

If we would have left of the string that describes the method in the describe block then the output would be a lost less clear and readable:

###### Some Spec

``` ruby

describe Agent do

  it 'returns one item, the favorite gadget of the agent ' do
    expect(agent.favorite_gadget).to eq 'Walther PPK' 
  end

end

```

###### Output

``` bash

Failures:

  1) Agent returns one item, the favorite gadget of the agent 
     Failure/Error: expect(agent.favorite_gadget).to eq 'Walther PPK'

```

Sure there are other ways to circumvent this, passing this info via your `it` block for example, but the other appraoch is just simple and works. Whatever makes your blood flow of course!

## Four Phases Of A Test

Best practices in testing recommends that we compose our tests in four distinctive phases:

+ Test setup
+ Test exercise
+ Test verification
+ Test teardown


These four phases are mostly for readability and to give your tests a ...??? structure. It’s a so called testing pattern, basically a practice that the community widely agreed upon to be useful and recommended. This whole topic is a deep rabbit whole, so know that I’ll leave out a bunch to not confuse and to not spend a parapgraph that has the length of a whole article.

+ Setup

During setup you prepare the scenario under which the test is supposed to run. In most cases this will include data that you set up to be ready for some kind of exercise. Little tip: don’t overcomplicate things and set up only the miniumm amount necessary to make the test work.

``` ruby

agent = Agent.create(name: 'James Bond')
mission = Mission.create(name: 'Moonraker', status: 'Green')

```

+ Exercise

This part actually runs the thing that you want to test in this spec. Could be as simple as:

``` ruby

status = mission.agent_status

```

+ Verify

Now you verify if your assertion about the test is being met or not. So you test the system against your own expectations.

``` ruby

expect(status).not_to eq 'MIO')

```

+ Teardown

The framework takes care of memory and database cleaning issues—a reset basically. Nothing for you to handle at this point. The goal is to get back a pristine state to run new tests without any surprises from the currently running ones.

Let’s see what this would mean in our concrete example:

###### Some Spec

``` ruby

describe Agent, '#favorite_gadget' do

  it 'returns one item, the favorite gadget of the agent ' do

  # Setup
    agent = Agent.create(name: 'James Bond')
    q = Quartermaster.create(name: 'Q') 
    q.technical_briefing(agent)

  # Exercise    
    favorite_gadget = agent.favorite_gadget

  # Verify
    expect(favorite_gadget).to eq 'Walther PPK' 

  # Teardown is for now mostly handled by RSpec itself
  end

end

```

As you can see, in this example here we separated the exercise and verify phases clearly from each other whilst in the other dummy examples above, `expect(agent.favorite_gadget).to eq 'Walther PKK` mixed both phases together. Both are valid scenarios and have their place. The newlines help additionally to visually separate how the test is structured.

## The Hard Thing About Testing

Now comes the hard part, what to test and how. In my opinion this is the aspect of testing that is most confusing to newcomers. And understandably so! You are new to the language and framework and often don’t even know yet what you don’t know. How do you write tests for that? Very good question. I’ll be very frank, you don’t—most likely—and you won’t for quite a while. Getting comfortable with this stuff takes a while. When you have a mentor or attend some bootcamp and such you have the opportunity to directly learn from experienced people. In that case, your progress in this department will be different of course.

On the other hand, if—like so many others out there—you are teaching yourself this stuff, patience will be key. Reading all the books and articles certainly get you in the right direction, but I feel like testing needs a lot of more advanced puzzle pieces in place in order for you to make complete sense and maybe even more important, before you feel comfortable with it. The “good” news is that this is not unusual and we all have been kinda there. Perserverance is important. You can do this! It’s no rocket science but it will take a while until you can view the process of writing an application from the other way around, from the perspective of tests I mean. For now, keep pushing, have fun, make mistakes, write apps, copy tutorials and what not until the light bulb goes off.

## Final Thoughts

When you write your individual tests, you want to make it do the simplest thing possible. Highly focused tests are really key. You want to design your application via very simple steps and then follow the errors your test suite is providing you with. Only implement what is necessary to get the app green. Not more, not less! That’s the “driven” part in Test-driven development. Your work is guided by the needs of your tests.





These sorts of helpers in RSpec, not surprisingly as everywhere else, help you to encapsulate and therefore reuse arbitrary methods that you will maybe need all over the place. `/helpers` is a nice home for this sort of stuff and keeps your spec files cleaner and more DRY. The other thing such helpers support is readability. When you don’t have to focus on repeated implementation details of some frequently ocurring setup or whatever, you can package them up into a nicely readable helper method and store it in a single “authoritive” place among other helpers. That let’s you and other focus about the details of the actual scenario under test and get’s non-relevant stuff out of the way. Be careful not to hide important implementation details that are necessary to understand each test. Too clever abstractions that invite so-called mystery guests for example can bite you in the long run. 

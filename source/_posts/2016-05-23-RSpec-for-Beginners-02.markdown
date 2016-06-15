---
layout: post
title: RSpec Testing for Beginners 02
date: 2016-05-23 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, TDD, BDD, Testing, Test-Driven-Development Ruby, Ruby on Rails]
---

{% img /images/RSpec/RSpec-Testing-Red-with-Logo.jpg %}

## Topics

+ Matchers
+ Let
+ Subjects
+ Callbacks
+ Generators
+ Tags

In the first article we have spent quite a lot of time trying to answer the “why?” of testing. I suggest we get right back to the “how?” and spare us any more context. We covered that part extensively already. Let’s see what else RSpec has to offer that you as a beginner can handle right away.

## Matchers

So this is gonna approach the heart of things. RSpec provides you with a ton of so-called matchers. These are your bread and butter when you write your expectations. So far you have seen `.to eq` and `.not_to eq`. But there is a much bigger arsenal to write your specs. You can test to raise errors, for truthy and falsy values or even for specific classes. Let’s run down a few options to get you started:

+ `.to eq`
+ `.not_to eq` 

This tests for equivalence.

###### Some Spec

``` ruby

...

it 'some clever description' do
  expect(agent.enemy).to eq 'Ernst Stavro Blofeld'
  expect(agent.enemy).not_to eq 'Winnie Pooh'
end

...

```

### Attention!

To keep things short, I packed two expect statements within one `it` block. It is good practice though to test only a single thing per test. This keeps things a lot more focused and your tests will end up less brittle when you change things.

+ ```.to be_truthy```
+ ```.to be true```

###### Some Spec

``` ruby

...

it 'some clever description' do
  expect(agent.hero?).to be_truthy
  expect(enemy.megalomaniac?).to be true
end

...

```

The difference is that ```be_truthy``` is true when it’s not `nil` or `false`. So it will pass if the result is neither of these two—kinda “true-like”. ``` to be true``` on the other hand only accepts a value that is ```true``` and nothing else.

+ ```.to be_falsy```
+ ```.to be false```

###### Some Spec

``` ruby

...

it 'some clever description' do
  expect(agent.coward?).to be_falsy
  expect(enemy.megalomaniac?).to be false
end

...

```

Similar to the two examples above, ```.to be_falsy``` expects either a `false` or a `nil` value and `to be false` will only do a direct comparison on `false`.

+ ```.to be_nil```
+ ```.to_not be_nil```

And last but not least, this tests exactly for `nil` itself. I spare you the example.

+ `.to match()`

I hope you already had the pleasure of looking into regular expressions. If not, this is a sequence of characters with which you can define a pattern that you put between two forward slashes to search strings. A regex can be very handy if you want to look for broader patterns that you could generalize in such an expression.

###### Some Spec

``` ruby

...

it 'some clever description' do
  expect(agent.number.to_i).to match(/\d{3}/)
end

...

```

Suppose we would be dealing with agents like James Bond, 007, who are assigned three digit numbers, then we could test for it this way—primitively here of course.

+ `>`
+ `<`
+ `<=`
+ `>=`

Comparisons come in handy more often than one might think. I assume the examples below will cover what you need to know.

###### Some Spec

``` ruby

...

it 'some clever description' do
  ...

  expect(agent.number).to be < quartermaster.number
  expect(agent.number).to be > m.number
  expect(agent.kill_count).to be >= 25 
  expect(quartermaster.number_of_gadgets).to be <= 5
end

...

```

Now, we are getting somewhere less boring. You can also test for classes and types:

+ ```.to be_an_instance_of```
+ ```.to be_a```
+ ```.to be_an```

###### Some Spec

``` ruby

...

it 'some clever description' do
  mission = Mission.create(name: 'Moonraker')
  agent = Agent.create(name: 'James Bond')
  mission.agents << agent

  expect(@mission.agents).not_to be_an_instance_of(Agent)
  expect(@mission.agents).to be_a(ActiveRecord::Associations::CollectionProxy)
end

...

```

In the dummy example above you can see that a list of agents that are associated with a mission are not of class `Agent` but of `ActiveRecord::Associations::CollectionProxy`. What you should take away from this one is that we can easily test for classes themselves while staying highly expressive. ```.to be_a``` and ```.to be_an``` do one and the same thing. You have both options available to keep things readable.

Testing for errors is also massively convenient in RSpec. If you are super fresh to Rails and not sure yet which errors the framework can throw at you, you might not feel the need to use these—of course, makes total sense. At a later stage in your development, you will find them very handy though. You have four ways to deal with them:

+ ```.to raise_error```

This is the most generic way. Whatever error is raised will be cast in your net.

+ ```.to raise_error(ErrorClass)```

That way you can specify which class the error exactly should come from.

+ ```.to raise_error(ErrorClass, "Some error message")```

This is even more fine grained since you not only mention the class of the error but a specific message that should be thrown with the error)

+ ```.to raise_error("Some error message)```

Or you just mention the error message itself without the error class. The expect part needs to be written a little bit different though, we need to wrap the part under text in a code block itself:

###### Some Spec

``` ruby

...

it 'some clever description' do
  agent = Agent.create(name: 'James Bond')

  expect{agent.lady_killer?}.to raise_error(NoMethodError)
  expect{double_agent.name}.to raise_error(NameError)
  expect{double_agent.name}.to raise_error("Error: No double agents around")
  expect{double_agent.name}.to raise_error(NameError, "Error: No double agents around")
end

...

```

+ ```.to start_with```
+ ```.to end_with```

Since we often deal with collections when building web apps, it’s nice to have a tool to peek into them. Here we added two agents, Q and James Bond and just wanted to know who comes first and last in the collection of agents for a particular mission—here Moonraker.

###### Some Agent Spec

``` ruby

...

it 'some clever description' do
  moonraker = Mission.create(name: 'Moonraker')
  bond = Agent.create(name: 'James Bond')
  q = Agent.create(name: 'Q')
  moonraker.agents << bond
  moonraker.agents << q
 
  expect(moonraker.agents).to start_with(bond)
  expect(moonraker.agents).to end_with(q)
end

..

```

+ `.to include`

This one is also helpful to check the contents of collections.

###### Some Agent Spec

``` ruby

...

it 'some clever description' do
  mission = Mission.create(name: 'Moonraker')
  bond = Agent.create(name: 'James Bond')
  mission.agents << bond
 
  expect(mission.agents).to include(bond)
end

...

```

+ predicate matchers

These predicate matchers are a feature of RSpec to dynamically create matchers for you. If you have predicate methods in your models for example (ending with a question mark), then RSpec knows that it should build matchers for you that you can use in your tests. In the example below, we want to test if an agent is James Bond:

###### Agent Model

``` ruby

class Agent < ActiveRecord::Base

  def bond?
    name == 'James Bond' && number == '007' && gambler == true
  end

  ...

end

```

Now, we can use this in our specs like so:

###### Some Agent Spec

``` ruby

...

it 'some clever description' do
  agent = Agent.create(name: 'James Bond', number: '007', gambler: true)

  expect(agent).to be_bond
end

it 'some clever description' do
  agent = Agent.create(name: 'James Bond')

  expect(agent).not_to be_bond
end

...

```

RSpec let’s us use the the method name without the question mark—to form a better syntax I suppose. Cool, ain’t it?

## Let

`let` and `let!` might look like variables at first, but they are actually helper methods. The first one is lazily evaluated, which means that it is only run and evaluated when a spec actually uses it and the other let with the bang(!) is run regardless of being used by a spec or not. Both versions are memoized and their values will be cached within the same example scope.

###### Some Spec File

``` ruby

describe Mission, '#prepare', :let do
  let(:mission)  { Mission.create(name: 'Moonraker') }
  let!(:bond)    { Agent.create(name: 'James Bond') }

  it 'adds agents to a mission' do
    mission.prepare(bond)
    expect(mission.agents).to include bond
  end
end

```

The bang version that is not lazily evaluated can be time consuming and therefore costly if it becomes your fancy new friend. Why? Because it will set up this data for each test in question, no matter what, and might eventually end up being one of these nasty things that slow down your test suite significantly.

You should know this feature of RSpec since `let` is widely known and used. That being said, the next article will show you some issues with it that you should be aware off. Use these helper methods with caution or at least in small doses for now.

## Subjects

RSpec offers you to declare the subject under test very explicitly. There are better solutions for this and we will discuss the downsides of this approach in the next article when I show a few things you generally want to avoid. But for now, let’s have a look at what `subject` can do for you:

###### Some Spec File

``` ruby

describe Agent, '#status' do
  subject { Agent.create(name: 'Bond')  }

  it 'returns the agents status' do
    expect(subject.status).not_to be 'MIA'
  end
end

```

This approach can on the one hand help you with reducing code duplication, having a protagonist declared once in a certain scope but it can also lead to something called a mystery guest. This simply means that we might end up in a situation where we use data for one of our test scenarios but have no idea anymore where it actually comes from and what it is comprised of. More on that in the next article.

## Callbacks

In case you are not aware about callbacks yet, let me give you a brief heads up. Callbacks are run at certain specific points in the life cycle of code. In terms of Rails this would mean that you have code that is being run before objects are created, updated, destroyed etc. In the context of RSpec, it’s the life cycle of tests being run. That simply means that you can specify hooks that should be run before or after each test is being run in the spec file for example—or simply around each test. There are a few more fine-grained options available but I recommend we avoid getting lost in the details for now. First things first:

+ `before(:each)`

This callback is run before each test example.

###### Some Spec file

``` ruby

describe Agent, '#favorite_gadget' do

  before(:each) do
    @gagdet = Gadget.create(name: 'Walther PPK')
  end

  it 'returns one item, the favorite gadget of the agent ' do
    agent = Agent.create(name: 'James Bond')
    agent.favorite_gadgets << @gadget

    expect(agent.favorite_gadget).to eq 'Walther PPK'
  end

  ...

end

```

Let’s say you would need a certain gadget for every test you run in a certain scope. `before` let’s you extract this into a block and prepares this little snippet for you conveniently. When you set up data that way, you have to use instance variables of course to have access to it among various scopes.

### Attention!

Don’t get fooled by convenience in this example. Just because you can do this kinda stuff does not mean you should. I want to avoid going into AntiPattern territory and confuse the hell out of you, but on the other hand I want to explain the downsides to these simple dummy exercises a bit as well. In the example above, it would be much more expressive if you set up the needed objects on a test-by-test basis. Especially on larger spec files, you can quickly loose sight of these little connections and make it harder for others to piece together these puzzles.

+ `before(:all)`

This before block runs only once before all the other examples in a spec file.
###### Some Spec file

``` ruby

describe Agent, '#enemy' do

  before(:all) do
    @main_villain = Villain.create(name: 'Ernst Stavro Blofeld')
    @mission = Mission.create(name: 'Moonraker')
    @mission.villains << @main_villain
  end

  it 'returns the main enemy Bond has to face in his mission' do
    agent = Agent.create(name: 'James Bond')
    @mission.agents << agent

    expect(agent.enemy).to eq 'Ernst Stavro Blofeld'
  end

  ...

end

```

When you remember the four phases of test, before blocks sometimes are helpful in setting something up for you that needs to be repeated on a regular basis—probably stuff that is a bit more meta in nature.


`after(:each)` and `after(:all)` have the same behaviour but are simply run after your tests have been executed. `after` is often used for cleaning up your files for example. But I think it’s a bit early to address that. So commit it to memory, know that it’s there in case you start needing it and let’s move on to explore other, more basic things.

All of these callbacks can be placed strategically to fit your needs. Place them in any `describe` block scope that you need to run them—they don’t have to necessarily be placed on top of your spec file. They can easily be nested way inside your specs. 

###### Some Spec file

``` ruby

describe Agent do
  before(:each) do
    @mission = Mission.create(name: 'Moonraker')
    @bond = Agent.create(name: 'James Bond', number: '007')
  end

  describe '#enemy' do 
    before(:each) do
      @main_villain = Villain.create(name: 'Ernst Stavro Blofeld')
      @mission.villains << @main_villain
    end

    describe 'Double 0 Agent with associated mission' do
      it 'returns the main enemy the agent has to face in his mission' do
        @mission.agents << @bond
  
        expect(@bond.enemy).to eq 'Ernst Stavro Blofeld'
      end
    end 

    describe 'Low-level agent with associated mission' do
      it 'returns no info about the main villain involved' do
        some_schmuck = Agent.create(name: 'Some schmuck', number: '1024')
        @mission.agents << some_schmuck

        expect(some_schmuck.enemy).to eq 'That’s above your paygrade!'
      end
    end

    ...

    ...

    ...

  end
end

```

As you can observe, you can place callback blocks at any scope to your liking, go as deep as you need. The code in the callback will be executed within the scope of any describe block scope. But a little bit of advice: if you feel the need of nesting too much and things seem to get a little bit messy and complicated, rethink your approach and consider how you could simplify the tests and its setup. KISS! Keep it simple, stupid.

Also, pay attention how nice this reads when we force these tests to fail:

###### Output

``` ruby

Failures:

  1) Agent#enemy Double 0 Agent with associated mission returns the main enemy the agent has to face in his mission
     Failure/Error: expect(@bond.enemy).to eq 'Ernst Stavro Blofeld'
     
       expected: "Ernst Stavro Blofeld"
            got: "Blofeld"

  2) Agent#enemy Low-level agent with associated mission returns no info about the main villain involved
     Failure/Error: expect(some_schmuck.enemy).to eq 'That’s above your paygrade!'
     
       expected: "That’s above your paygrade!"
            got: "Blofeld"

```

## Generators

Let’s also have a quick look at what generators are provided by RSpec for you. You have already seen one when we used `rails generate rspec:install`. This little fella made setting up RSpec for us quick and easy. What else do we have?

+ `rspec:model`

Want to have another dummy model spec?


###### Terminal

``` bash

rails generate rspec:model another_dummy_model

```

###### Output

``` bash

create  spec/models/another_dummy_model_spec.rb

```

Quick, isn’t it? Or a new spec for a controller test for example:

+ `rspec:controller`

###### Terminal

``` bash

rails generate rspec:controller dummy_controller

```

###### Output

``` bash

spec/controllers/dummy_controller_controller_spec.rb

```

+ `rspec:view`

Same works for views of course. We won’t be testing any views like that though. Specs for views give you the least bang for the buck and it is totally sufficient in probably almost any scenario to inderectly test your views via feature tests. Feature tests is not a specialty of RSpec per se and more suited to another article on its own. That being said, if you are curious, check out [Capybara](http://jnicklas.github.io/capybara/), which is an excellent tool for that kind of thing. It let’s you test whole flows that exercise multiple parts of your app coming together—testing complete features while simulating the browser experience. For example, a user who pays for multiple items in a shopping cart.

+ rspec:helper

The same generator strategy let’s us also place a helper without much fuzz.

###### Terminal

``` bash

rails generate rspec:helper dummy_helper

```

###### Output

``` bash

create  spec/helpers/dummy_helper_helper_spec.rb

```

The double ```helper_helper``` part was not an accident. When we give it a more “meaningful” name, you will see that RSpec just attaches ```_helper``` on its own.

###### Terminal

``` bash

rails generate rspec:helper important_stuff

```

###### Output

``` bash

create  spec/helpers/important_stuff_helper_spec.rb

```

### A Word About Helpers

No, this directory is not a place to hord your precious helper methods that come up while refactoring your tests. These would go under `spec/support` actually. `spec/helpers` is for the tests that you should write for your view helpers—a helper like `set_date` would be a common example. Yes, complete test coverage of your code should also include these helper methods. Just because they often seem small and trivial doesn’t mean that we should overlook them or ignore their potential for bugs we wanna catch. The more complex the helper actually turns out, the more reason you should have to write a `helper_spec` for it!

Just in case you start playing around with it right away, keep in mind that you need to run your helper methods on a `helper` object when you write your helper tests in order to work. So they can only be exposed using this object. Something like this:

###### Some Helper Spec

``` ruby

...

describe '#set_date' do

...

  helper.set_date

...

end
 
...

```

You can use the same kind of generators for feature specs, integration specs and mailer specs. These are out of our scope for today but you can commit them to memory for future use:

+ rspec:mailer
+ rspec:feature
+ rspec:integration

### A Look At Generated Specs

The specs we created via the generator above are ready to go and you can add your tests in there right away. Let’s have a tiny look at a difference between specs though:

###### spec/models/dummy_model_spec.rb

``` ruby
require 'rails_helper'

RSpec.describe DummyModel, type: :model do
  pending "add some examples to (or delete) #{__FILE__}"
end

```

###### spec/controllers/dummy_controller_controller_spec.rb

``` ruby

require 'rails_helper'

RSpec.describe DummyControllerController, type: :controller do
end

```

###### spec/helpers/dummy_helper_helper_spec.rb

``` ruby

require 'rails_helper'

RSpec.describe DummyHelperHelper, type: :helper do
  pending "add some examples to (or delete) #{__FILE__}"
end

```

It doesn’t need a wunderkind to figure out that they all have different types. This `:type` RSpec metadata gives you an opportunity to slice and dice your tests across file structures. You can target these tests a bit better that way. Say you want to have some sort of helpers only loaded for controller specs for example. Another example would be that you want to use another directory structure for specs that RSpec does not expect. Having this metadata in your tests makes it possible to continue to use RSpec support functions and not trip up the test suite. So you are free to use whatever directory structure that works for you if you add this `:type` metadata.

Your standard RSpec tests do not depend on that metadata on the other hand. When you use these generators, they will be added for free but you can totally avoid them as well if you don’t need them. You can also use this metadata for filering in your specs. Say you have a before block that should run only on model specs for example. Neat! For bigger test suites, this might come in very handy one day. You can filter which focused group of tests you want to run—instead of executing the whole suite which might take a while. Your options extend beyond the three tagging options above of course. Let’s learn more about slicing and dicing your tests in the next section.

## Tags

When you amass a bigger test suite over time, it won’t just be enough to run tests in certain folders to run RSpec tests quickly and efficiently. What you want to able to is run tests that belong together but might be spread across multiple directories. Tagging to the rescue! Don’t get me wrong, organizing your tests smartly in your folders is key as well, but tagging takes this just a bit further.

You are giving your tests some metadata as symbols like “:wip”, “:checkout” or whatever fits your needs.  When you run these focused groups of tests you simply specify that RSpec should ignore running other tests this time by providing a flag with the name of the tags.

###### Some Spec File

``` ruby

describe Agent, :wip do

  it 'is a mess right now' do
    expect(agent.favorite_gadgets).to eq 'Unknown'
  end

end

```

###### Terminal

``` bash

rspec --tag wip

```

###### Output

``` bash

Failures:

  1) Agent is a mess right now
       Failure/Error: expect(agent.favorite_gadgets).to eq 'Unknown'

...

```

You could also run all kinds of tests and ignore a bunch of groups that are tagged a certain way. You just provide a tilde (~) in front of the tag name and RSpec is happy to ignore these tests.

###### Terminal

``` bash

rspec --tag ~wip

```

Running multiple tags synchronously is not a problem either:

###### Terminal

``` bash

rspec --tag wip --tag checkout

rspec --tag ~wip --tag checkout

```

As you can see above, you can mix and match them at will. The syntax is not perfect, repeating `--tag` is maybe not ideal, but hey, it’s not biggie either! Yes all of this is a bit more extra work and mental overhead when you compose the specs, but on the flip side, it really provides you with a powerful ability to slice up your test suite on demand. On bigger projects it can save you a ton of time that way.

## Final Thoughts

What you learned so far should equip you with the absolute basics to play with tests on your own—a survival kit for beginners. And really do play and make mistakes as much as you can. Take RSpec and the whole test-driven thingie for a spin and don’t expect to write quality tests right away. There are still a couple of pieces missing before you will feel comfortable and before you will be effective with it. For me personally this was a bit frustrating in the beginning because it was hard to see how to test something that I haven’t yet implemented and something that I didn’t fully understand how it would behave. 

Testing really proves if you understood a framework like Rails and know how the pieces fit together. When you write tests you will need to be able to write expectations for how a framework should behave. That is not easy if you started out with all of this. Dealing with multiple domain specific languages, here RSpec and Rails for example, plus learning the Ruby API can be confusing as hell. Don’t feel bad if the learning curve seems daunting, it will get easier if you stick with it. Making this light bulb go off won’t happen over night, but to me it was very much worth the effort.


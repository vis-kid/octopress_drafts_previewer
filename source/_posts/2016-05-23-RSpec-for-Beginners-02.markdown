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

+ Callbacks
+ Generators
+ Tags

## Callbacks

In case you are not aware about callbacks yet, let me give you a brief heads up. Callbacks is code that is run at certain specific points in the life cycle of code. In terms or Rails this would mean that you have code that is being run before objects are created, updated, destroyed etc. In the context of RSpec, it’s the life cycle of tests being run. That simply means that you can specify hooks that should be run before each test is run in the spec file, after all thats in that context have been executed or simply around each test. There are a few more fine-grained options available but I recommend we avoid getting lost in the details with them. First things first:

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

    expect(agent.favorite_gadgets).to eq 'Walther PPK'
  end

  ...

end

```

Let’s say you would need a certain gadget for every test you run in a certain scope. Before let’s you extract this into a before block and prepares this little snippet for you conveniently. When you set up data that way, you have to use instance variables of course to have access to among various scopes.


### Attention!

Don’t let yourself blind by convenience in this example. Just because you can do this kinda stuff does not mean you should. I want to avoid going into AntiPattern territory and confuse the hell out of you, but on the other hand I wanted to avoid showing you examples without explaining the downsides to these simple dummy exercises. In the example above, it would be much more expressive if you set up the needed objects on a test-by-test basis. Especially on larger spec files, you can quickly loose sight of these little connections and make it harder for others to piece together these puzzles.

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

When you remember the four phases of test, before blocks sometimes are helpful in setting something up for you that needs to repeated on a regular basis.


`after(:each)` and `after(:all)` have the same behaviour but are simply run after your tests have been executed. After is often used for cleaning up your files for example. But I think it’s a bit early to address that. So commit it to memory, tknow that it’s there in case you start to need it and let’s move on to explore other, more basic things.

All of these callbacks can be places strategically to fit your needs. Place them in any `describe` block scope that you need to run them—they don’t have to necessarily be placed on top of your spec file. They can easily be nested  way inside your specs. 

###### Some Spec file

``` ruby

describe Agent, :nested do
  before(:each) do
    @mission = Mission.create(name: 'Moonraker')
  end

  describe '#enemy' do 
    before(:each) do
      @main_villain = Villain.create(name: 'Ernst Stavro Blofeld')
      @mission.villains << @main_villain
    end

    describe 'with associated mission' do
      it 'returns the main enemy Bond has to face in his mission' do
        @agent = Agent.create(name: 'James Bond')
        @mission.agents << @agent
  
        expect(@agent.enemy).to eq 'Ernst Stavro Blofeld'
      end
    end 

    describe 'without associated mission' do
      it 'returns negative mission assignment statement' do
        @agent = Agent.create(name: 'Some schmuck')

        expect(@agent.enemy).to eq 'No mission assigned'
      end
    end
  end

  describe '#aborted_missions' do
    ...
  end

  describe '#double_o_missions' do
    ...
  end
end

```

As you can observe, you can place callback blocks at any scope to your liking, go as deep as you need. The code in the callback will be executed within the scope of any describe block scope. But a little bit of advice: if you feel the need of nesting too much and things seem to get a little bit messy and complicated, rethink your approach and consider how you could simplify the tests and its setup. KISS! Keep it simple, stupid.

Also, pay attention how nice this reads when we force these tests to fail:

###### Output

``` ruby

Failures:

  1) Agent#enemy with associated mission returns the main enemy Bond has to face in his mission
     Failure/Error: expect(@agent.enemy).to eq 'Ernst Stavro Blofeld'
     
       expected: "Ernst Stavro Blofeld"
            got: "Blofeld"

2) Agent#enemy without associated mission returns negative mission assignment statement
     Failure/Error: expect(@agent.enemy).to eq 'No mission assigned'
     
       expected: "No mission assigned"
            got: "Mission assigned"

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

Same works for views of course. We won’t be testing any views like that though. Specs for views give you the least bang for the buck and it is totally sufficient in probably almost any scenario to inderectly test your views via feature tests. Feature tests is not a specialty of RSpec per se and more suited to another article on its own. That being said, if you are curious, check out [Capybara](http://jnicklas.github.io/capybara/), which is an excellent tool for that kind of thing and let’s you test whole flows that exercise the whole app together. Therefore a feature tests because it can test multiple parts of your app coming together, exercising complete featureswhile simulating the browser experience—a user who pays for multiple items in a shopping cart for example.

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

No, this directory is not a place to hord your precious helper methods that come up while refactoring your tests. These would go under `spec/support` actually. `spec/helpers` is for the tests that you should write for your view helpers—a helper like `set_date` would be a common example. Yes, complete test coverage of your code should also include these helper methods. Just because the often seem small and trivial doesn’t mean that we should overlook them or ignore their potential for bugs we wanna catch. The more complex the helper actually turns out, the more reason you should have to write a `helper_spec` for it!

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

### The Little Differences

All these specs are ready to go and you can add your tests in there right away. Let’s have a tiny look at a difference between specs though:

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

It doesn’t need a wunderkind to figure out that they all have differnt types. This `:type` RSpec metadata gives you an opportunity to slice and dice your tests across file structures. You can target these tests a bit better that way. Say you want to have some sort of helpers only loaded for controller specs for example. Another example would be that you want to use another directory structure for specs that RSpec does not expect. Having this metadata in your tests makes it possible to continue to use RSpec support functions and not trip up the test suite. So you are free to use whatever directory structure that works for you if you add this `:type` metadata.

Your standard RSpec tests to not depend on that metadata on the other hand. When you use these generators, they will be added for free but you can totally avoid them as well if you don’t need them.

You can also use this metadata for filering in your specs. Say you have a before block that should run only on model specs for example. Neat!

For bigger test suites, this might come in very handy one day. You can filter which focused group of tests you want to run—instead of executing the whole suite which might take a while. Your options extend beyond the three tagging options above of course.

## Tags

When you amass a bigger test suite over time, it won’t just be enough to run tests in certain folders to run RSpec tests quickly and efficiently. What you want to able to is run tests that belong together but might be spread across multiple directories. Tagging to the rescue! Don’t get me wrong, organizing your tests smartly in your folders is key as well, but tagging let’s take this just a bit further.

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






These sorts of helpers in RSpec, not surprisingly as everywhere else, help you to encapsulate and therefore reuse arbitrary methods that you will maybe need all over the place. `/helpers` is a nice home for this sort of stuff and keeps your spec files cleaner and more DRY. The other thing such helpers support is readability. When you don’t have to focus on repeated implementation details of some frequently ocurring setup or whatever, you can package them up into a nicely readable helper method and store it in a single “authoritive” place among other helpers. That let’s you and other focus about the details of the actual scenario under test and get’s non-relevant stuff out of the way. Be careful not to hide important implementation details that are necessary to understand each test. Too clever abstractions that invite so-called mystery guests for example can bite you in the long run. 









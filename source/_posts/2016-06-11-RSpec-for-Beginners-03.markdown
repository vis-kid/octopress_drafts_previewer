---
layout: post
title: RSpec Testing for Beginners 03
date: 2016-06-11 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, TDD, BDD, Testing, Test-Driven-Development Ruby, Ruby on Rails]
---

{% img /images/RSpec/RSpec-Testing-Red-with-Logo.jpg %}

## Topics

+ Test Speed
+ Database Bottlenecks
+ Spring Preloader
+ Iffy RSpec Conveniences
+ Mystery Guests
+ Inline Code
+ Extract Methods

Now that you have the basics under your belt, we should take the time to discuss a few iffy parts of RSpec and TDD—a few issues that can easily be overused and some downsides of using parts of RSpec’s DSL unreflected. I want to avoid stuffing a lot of advanced concepts in your freshly hatched TDD brains but I feel a few points need to be made before you go on your first testing spree. Also, creating a slow test suite due to bad habits that are easily avoidable is something you can improve as a beginner right away.

Sure, there are quite a few things that you need to get more experience with before you will feel comfortable and effective with testing, but I bet you will also feel better from the start if you take away some of the best practices that improve your specs by manifold and are not stretching your skills too much right now. It is also a small window into more advanced concepts that you will need to pick up over time to “master” testing. I feel like I shouldn’t bother you too much at the start with these because it might just feel convoluted and confusing before you have developed the bigger picture that ties everything together neatly.

## Test Speed

Let’s start with speed. A fast suite is nothing that happens by accident, it is a matter of “constant” maintenance. Listening to your tests very frequently is pretty important—at least if you are on board with TDD and have been drinking the cool aid for a while—and fast test suites make it a lot more reasonable to pay attention to where the tests are guiding you.

Test speed is something you should take good care of. It is essential to make testing a regular habit and keep it fun. You want to be able to quickly run your tests so that you get rapid feedback while you are developing. The longer it takes to exercise the test suite, the more likely it will be that you skip testing more and more until you only do it at the end before you wanna ship a new feature. 

That might not sound that bad at first but this is not a trivial issue. One of the main benefits of a test suite is that it guides the design of your application—to me this is probably the biggest win from TDD. Longer test runs makes this part pretty much impossible because it’s very likely that you won’t run them to not break your flow. Speedy tests guarantee that you have no reason not to run your tests.

You can see this process as a dialogue between you and the test suite. If this conversation gets too slow it’s really painful to continue. When your code editor offers the possibility to also run your tests you should definitely make use of this feature. This will dramatically increase the speed and improve your workflow. Switching every time between your editor and a shell to run your tests gets old very quickly. But since these articles are targeted at newbie programmers I don’t expect you to set up your tools like this right away. There are other ways you can improve this process without needing to tinker with your editor right away. It’s good to know though and I recommend making such tools part of your workflow.

Also, be aware that you already learned how to slice your tests and that you don’t need to run the full test suite all the time. You can easily run single files or even single `it` blocks—all within a capable code editor without ever leaving it for the terminal. You can focus the test on the line under test for example. That feels like magic to be frank—it never gets boring.

## Database Bottlenecks

Writing too much to the database—often very unnecessarily so—is one sure way to quickly slow down your test suite—significantly I should add. In many test scenarios you can fake out the data that you need to set up a test and just focus on the data that is directly under test. You don’t need to hit the database for all of it most of the time—especially not for parts that are not under test directly and only support the test somehow. A logged in user while you are testing the amount to pay at a checkout for example. The user is like an extra that can be faked out. 

You should try to get away with not hitting the database as much as possible because this bites a big chunk out of a slow test suite. Also, try to not set up too much data if you don’t need it at all. That can be very easy to forget with integration tests especially. Unit tests are often a lot more focused by definition. This strategy will prove very effective in avoiding to slow down test suites over time. Choose your dependencies with great care and see what is the smallest amount of data that gets your tests to pass.

I don’t want to go into any more specifics for now, it’s probably a bit too early in your trajectory to talk about stubs, spies, fakes and stuff. Confusing you here with such advanced concepts seems counterproductive and you will run into these soon enough. There are many strategies for speedy tests that also involve other tools than RSpec. For now, try to wrap your head around the bigger picture with RSpec and testing in general.

You also want to aim to test everything only once—if possible. Don’t re-test the same thing over and over again, that is just wasteful. This mostly happens by accident and / or bad design decisions. If you started to have tests that are slow, this is an easy place to refactor to get a speed boost.

The majority of your tests should also be on the Unit level, testing your models. This will not only keep things speedy but will also provide you with the biggest bang for the buck. Integration tests which test whole workflows—imitating the user’s behaviour to a degree by bringing together a bunch of components and testing them synchronously—should be the smallest part of your testing pyramid. These are rather slow and “expensive”. Maybe ten percent of your overall tests is not unrealistic to shoot for—but this depends I guess.

Exercising the database as little as possible can be hard because you need to learn quite a few more tools and techniques to achieve that effectively but it is essential to grow test suites that are reasonably fast—fast enough to really run your tests frequently.

## Spring Preloader

The Spring server is a feature of Rails and preloads your application. This is another straightforward strategy to increase your test speed significantly—right out of the box I should add. What it does is simply keep your application running in the background without needing to boot it with every single test run. Btw, the same applies for Rake tasks and migrations, these will run faster as well.

Since Rails 4.1 Spring was included in Rails—added to the Gemfile automatically—and you don’t need to do much to start or stop this preloader. In the past we had to wire up our own tools of choice for this—which you can still do of course if you have other preferences. What is really nice and thoughtful is that it will restart automatically if you change some gems, initializers of config files—a nice and handy touch because it’s easy to forget to take care of it yourself.

By default it is configured to run `rails` and `rake` commands only. So we need to set it up to also run with the `rspec` command for running our tests. You can ask for the status of spring like so:

###### Terminal

``` bash

spring status

```

###### Output

``` bash

Spring is not running.

```

Since the output told us that Spring is not running you simply start it with `spring server`. When you now run `spring status` you should see something similar to this:

###### Output

``` bash

Spring is running:

3738 spring server | rspec-dummy | started 21 secs ago

```

Now we should check what Spring is set up to preload actually.

###### Terminal

``` bash

spring binstub --all

```

###### Output

``` bash

* bin/rake: spring already present
* bin/rails: spring already present

```

This tells us that Spring is preloading Rails for `rake` and `rails` commands, nothing else so far. That we need to take care of. We need to add the gem `spring-commands-rspec` and our tests are then ready to be preloaded.

###### Gemfile

``` ruby

gem 'spring-commands-rspec', group: :development

```

###### Terminal

``` bash

bundle install

bundle exec spring binstub rspec

```

I spare you the output from `bundle install`, I’m sure you have seen it more than your fair share already. Running `bundle exec spring binstub rspec` on the other hand generates a `bin/rspec` file which basically adds it to be preloaded by Spring. Let’s see if this worked:

###### Terminal

``` bash

spring binstub --all

```

This created something called a binstub—a wrapper for executables like `rails`, `rake`, `bundle`, `rspec` and such—so that when you use the `rspec` command it will use Spring. As an aside, such binstubs ensure that you are running these executables in the right environment. They also let you run these commands from every directory in your app—not just from the root. The other advantage of binstubs is that you don’t have to prepend `bundle exec` with everything.

###### Output

``` bash

* bin/rake: spring already present
* bin/rspec: spring already present
* bin/rails: spring already present

```

Looks A O.K.! Let’s stop and restart the Spring server before we move on:

###### Terminal

``` bash

spring stop

spring server

```

So now you run the spring server in one dedicated terminal window and you run your tests with a slightly different syntax in another. We simply need to prefix every test run with the `spring` command:

###### Terminal

``` bash

spring rspec spec

```

This runs all your spec files of course. But no need to stop there. You can also run single files or tagged testes via Spring—no problem! And they all will be lightening fast now, on smaller test suites they really seem almost instantaneous. On top of that, you can use the same syntax for your `rails` and `rake` commands. Nice eh?

###### Terminal

``` bash

spring rake

spring rails g model BondGirl name:string

spring rake db:migrate

...

```

So, we get Spring out of the box to speed things up in Rails but we must not forget to add this little Gem to let Spring know how to play ball with RSpec.

## Iffy RSpec Conveniences

The things mentioned in this section are probably good to avoid as long as you can find another solution for them. Overusing a few of the RSpec conveniences can lead to developing bad testing habits—at the very least iffy ones. What we will discuss here is convenient on the surface but might bite you a bit later down the road.

They should not be considered AntiPatterns—things to avoid straight away—but rather seen a “smells”, things that you should be careful about and which might introduce a significant cost you often don’t want to pay. The reasoning for this involves a few more ideas and concepts that you as a beginner are most likely not familiar with yet—and quite frankly might be a bit over your head yet at this point—but I should at least send you home with a few red flags to think about and commit to memory for now.

+ let

Having a lot of `let` references can seem very convenient at first—especially because they DRY things up quite a bit. It seems like a reasonably good extraction at first to have them at the top of your files for example. On the other hand, they can easily give you a hard time understanding your own code if you visit specific tests some significant amount of time later. Not having the data set up within your `let` blocks does not aid the understanding of your tests too much. That is not as trivial as it may sound at first, especially if other developers are involved who need to read your work as well.

This sort of confusion becomes a lot more expensive the more developers are involved. It’s not only time consuming if you have to hunt down `let` references over and over again, it is also stupid because it would have been avoidable with very little effort. Clarity is king, no doubt about it. Another argument to keep this data inline is that your test suite will be less brittle. You don’t want to build a house of cards that becomes more unstable with every `let` that is hiding away details from each test. You probably learned that using global variables is not a good idea. In that sense, `let` is semi global within your spec files.

Another issue is that you will need to test a lot of different variations, different states for similar scenarios. You will soon run out of reasonably named `let` statements to cover all the different versions you might need—or end up with a haystack of tons of similar named state variations. When you set the data up in every test directly, you don’t have that problems. Local variables are cheap, highly readable and don’t mess with other scopes—they can be even more expressive because you don’t need to consider tons of other tests that might have a problem with a particular name. You want to avoiding creating another DSL on top of the framework that everybody needs to decipher for each test that is using `let`. I hope that feels very much like a waste of everybody’s time.

+ before & after

Save things like `before`, `after` and its variations for special occasions and don’t use it all the time, all over the place. See it as one of the big guns you pull out for meta stuff. Cleaning up your data is a good example that is too meta for each individual test to deal with. You want to extract that of course.

## Mystery Guests

Often you put the `let` stuff at the top of a file and hide away these details from other tests that use them going down the file. You want to have the relevant information and data as close as possible to the part where you actually exercise the test—not miles away making individual tests more obscure. In the end, it feels like too much rope to hang yourself because `let` introduces widely shared fixtures. That basically breaks down to dummy test data whoose scope is not tight enough. This easily leads to one major smell called “mystery guest”. That means that you have test data showing up out of nowhere or is simply being assumed. You will often need to hunt them down first to understand a test—especially if some time has passed since you wrote the code or if you are new to a codebase. It is much more effective to define your test data inline exactly where you need it—in the setup of a particular test and not in a much broader scope.

###### Agent Spec

``` ruby

...

...

...

describe Agent, '#print_favorite_gadget' do
  it 'prints out the agents name, rank and favorite gadget' do
    expect(agent.print_favorite_gadget).to eq('Commander Bond has a thing for Aston Martins')
  end
end

```

When you look at this it reads quite nice right? It is succinct, a one-liner, pretty clean no? Let’s not fool ourselves. This test does not tell us much about the `agent` in question and it does not tell us the whole story. The implementation details are important but we are not seeing any of it. The agent seems to have been created some place else and we’d have to hunt it down first in order to fully understand what’s going on here. So it maybe looks elegant on the surface but it comes with a hefty price.

Yes, your tests might not end up being super DRY all the time in that regard, but this is a little price to pay for being more expressive and easier to understand I think. Sure there are exceptions, but they should really be merely applied to exceptional circumstances after you exhausted options pure Ruby offers right away. With a mystery guest you have to find out where data comes from, why it matters and what its specifics really are. Not seeing the implementation details in a particular test itself just makes your live harder than it needs to. I mean do what you feel like if you work on your own projects, but when other developers are involved, it would be nicer to think about making their experience with your code as smooth as possible. As with many things of course, the essential stuff lies in the details and you don’t want to keep yourself and others in the dark about those. Readability, succinctness and the convenience of `let` should not come at the cost of loosing clarity of implementation details and misdirection. You want each individual test to tell the full story and provide all the context to understand it right away.

## Inline Code

Long story short, you want to have tests that are easy to read and easier to reason about—on a test-by-test-basis. Try to specify everything you need in the actual test—and not more than that. This kind of waste starts to “smell” just like any other sort of junk. That also implies that you should add the details you need for specific tests as late as possible—when you create test data overall, within the actual scenario and not some place remote. The suggested use of `let` offers a different kind of convenience that seems to oppose this idea.

Let’s have another go with the previous example and implement it whout the mystery guest issue. In the solution below we’ll find all the relevant info for the test inline. We can stay right in this spec if it fails and don’t need to look for additional info some place else.

###### Agent Spec

``` ruby

...

...

...

describe Agent, '#print_favorite_gadget' do
  it 'prints out the agents name, rank and favorite gadget' do
    agent = Agent.new(name: 'James Bond', rank: 'Commander', favorite_gadget: 'Aston Martin')

    expect(agent.print_favorite_gadget).to eq('Commander Bond has a thing for Aston Martins')
  end
end

```

It would be nice if `let` lets you set up barebones test data that you can enhance on a need to know basis in each specific test, but this is not how `let` is rolling. That is how we use factories via [Factory Girl](https://github.com/thoughtbot/factory_girl) these days. I will spare you the details, especially since I have written a few pieces about it already. Here is my newbie-tailored articles [101](https://code.tutsplus.com/articles/factory-girl-101--cms-25087), [201](https://code.tutsplus.com/articles/factory-girl-201--cms-25171) about what Factory Girl has to offer—if you are already curious about that. It is written for developers without tons of experience as well.

Let’s look at another simple example that makes good use of supporting test data that is set up inline:

###### Agent Spec

``` ruby

describe Agent, '#current_mission' do

  it 'prints out the agent’s current mission status and its objective' do
    mission_octopussy = Mission.new(name: 'Octopussy', objective: 'Stop bad white dude')
    bond = Agent.new(name: 'James Bond', status: 'Undercover operation', section: '00', licence_to_kill: true)
    bond.missions << mission_octopussy

    expect(bond.current_mission).to eq ('Agent Bond is currently engaged in an undercover operation for mission Octopussy which aims to stop bad white dude')
  end
end

```

As you can see, we have all the information this tests need in one place and don’t need to hunt down any specifics some place else. It tells a story and is not obscure. As mentioned, this is not the best strategy for DRY code. The payoff is good though. Clarity and readability outweighs this little bit or repetitive code by a long shot—especially in large codebases.

For example, say you write some new, seemingly unrelated feature and suddenly this tests starts to fail as collateral damage and you haven’t touched this spec file in ages. Do you think you will be happy if you need to decypher the setup components first before you can understand and fix this failing test before you can continue with a completely different feature you are working on? I think not! You want to get out of this “unrelated” spec asap and get back to finishing the other feature. When you find all the test data right there where your tests tells you where it fails, you increase your chances by a long shot of fixing this quickly without “downloading” a completely different part of the app into your brain.

## Extract Methods

You can clean and DRY your code significantly by writing your own helper methods. No need to use RSpec DSL for something so cheap than a Ruby method. Let’s say you found a couple of repetitive fixtures that start to feel a bit dirty. Instead of going with a `let` or a `subject`, define a method at the bottom of a describe block—a convention—and extract the commonalities into it. If it is used a bit more widely within a file you can place it at the bottom of the file as well. A nice side effect is that you are not dealing with any semi-global variables that way. You will also save yourself from making a bunch of changes all over the place if you need to tweak the data a bit. You now can go to one central place where the method is defined and affect all the places it is used at once. Not bad!

###### Agent Spec

``` ruby

describe Agent, '#current_status' do

  it 'speculates about the an agent’s choice of destination if status is vacation' do
    bond = Agent.new(name: 'James Bond', status: 'On vacation', section: '00', licence_to_kill: true)

    expect(bond.current_status).to eq ('Commander Bond is on vacation, probably in the Bahamas')
  end

  it 'speculates about the an quartermasters’s choice of destination if status is vacation' do
    q = Agent.new(name: 'Q', status: 'On vacation', section: '00', licence_to_kill: true)

    expect(q.current_status).to eq ('The quartermaster is on vacation, probably at DEF CON')
  end
end

```

As you can see there is a bit of repetitive setup code and we want to avoid writing this over and over again. Instead we want to only see the essentials for this test and have a method build the rest of the object for us.

###### Agent Spec

``` ruby

describe Agent, '#current_status' do

  it 'speculates about the an agent’s choice of destination if status is vacation' do
    bond = build_agent_on_vacation('James Bond', 'On vacation')

    expect(bond.current_status).to eq ('Commander Bond is on vacation, probably in the Bahamas')
  end

  it 'speculates about the an quartermasters’s choice of destination if status is vacation' do
    q = build_agent_on_vacation('Q', 'On Vacation')

    expect(q.current_status).to eq ('The quartermaster is on vacation, probably at DEF CON')
  end

  def build_agent_on_vacation(name, status)
    Agent.new(name: name, status: status, section: '00', licence_to_kill: true)
  end
end

```

Now our extracted method takes care of the `section` and ```licence_to_kill``` stuff and thereby does not distract us from the essentials of the test. Of course this is a dummy example but you can scale its complexity as as much as you need. The strategy does not change. It is a very simple refactoring technique—that’s why I introduce it this early—but one of the most effective one. Also, it makes it almost a no-brainer to avoid the extraction tools RSpecs offers.

What you should also pay attention to is how expressive these helper methods can be without paying any extra price.

## Final Thoughts

Avoiding a few parts of the RSpec DSL and making good use of good ol’ Ruby and Object Oriented Programming principles is a good way to approach writing your tests. You can freely use the essentials of course, `describe`, `context` and `it` of course. Find a good reason to use other parts of RSpec and avoid them as long as you can. Just because things may seems convenient and fancy is not a good enough reason to not keep things more simple. Simple is good, it keeps your tests healthy and fast.


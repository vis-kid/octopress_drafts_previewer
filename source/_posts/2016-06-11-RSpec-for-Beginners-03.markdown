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
## Spring Preloader

+ Test Speed
+ Database Bottlenecks
+ Spring Preloader

## Test Speed

The test speed is important for a couple of reasons. You want to be able to quickly run your tests so that you get rapid feedback while you are developing. The longer it takes to exercise the test suite, the more likely it will be that you skip testing more and more until you only do it at the end before you wanna ship a new feature. 

That might not sound that bad at first but this is not a trivial issue. One of the main benefits of a test suite is that it guides the design of your application—to me this is probably the biggest win from TDD. Longer test makes this part pretty much impossible because it’s very likely that you won’t run them to not break your flow. Speedy tests guarantee that you have no reason not to run your tests.

You can see this process as a dialogue between you and the test suite. If this conversation gets too slow it’s really painful to continue. When your code editor offers the possibility to also run your tests you should definitely make use of this feature. This will dramatically increase the speed and improve your workflow. Switching every time between your editor and a shell to run your tests gets old very quickly. But since these articles are targeted at newbie programmers I don’t expect you to set up your tools like this right away. There are other ways you can improve this process without needing to tinker with your editor right away. It’s good to know though and I recommend making this part of your workflow.

Also, be aware that you already learned how to slice your tests and that you don’t need to run the full test suite all the time. You can easily run single files or even single `it` blocks—all within a capable code editor without ever leaving it for the terminal. You can focus the test on the line under test for example. That feels like magic to be frank, it never gets boring.

Listening to your tests is pretty important—at least if you are on board with TDD and have been drinking the cool aid for a while—and fast test suites make it a lot more reasonable to pay attention to where the tests are guiding you.

## Database Bottlenecks

Writing too much to the database—often very unnecessarily—is one sure way to quickly slow down your test suite. In many test scenarios you can fake out the setup data that you need to prepare a test and the data that is directly under test. You don’t need to hit the database for all of that most of the time. Try to get away not hitting the database as much as possible and also not set up too much data if you don’t need it at all. This will prove very effective to not create slow test suites over time. In other words, choose your dependencies wisely and see what is the smallest amount of data that get’s your tests to pass.

I don’t want to go into any more specifics on this for now, it’s probably a bit too early in your trajectory to talk about stubs, spies, fakes and stuff. Confusing you here with such advanced concepts seems counterproductive and you will run into these soon enough. Just be aware that avoiding to hit the database bites a big chunk out of your slow test suite and focus for now to wrap your head around the bigger picture with RSpec and testing.


Use the database as little as possible. This can be hard because you need to learn even more tools and techniques to achieve that but it is essential to grow test suites that are reasonably fast—fast enough to really run your tests frequently.

You also want to test everything only once if possible. Don’t retest the same thing over and over again. This mostly happens by accident and / or bad design decisions. If you started to have tests that are slow, this is an easy place to look for refactorings to get a speed boost.

The majority of your tests should also be on the Unit level, testing your models. Integration tests which tests whole workflows, bringing together a bunch of components and test them synchronously and thereby imitating the users behaviour to a degree, should be the smallest part of your testing pyramid.

## Spring Preloader

The Spring server is a feature of Rails and preloads your application. This increases your test speed significantly, right out of the box. What it does is simply keep your application running in the background without needing it to boot it with every single test run. Btw, the same applies for Rake tasks and migrations, these will run faster as well. Since Rails 4.1 this was included in Rails—added to the Gemfile automatically—and you don’t need to do any setup to start or stop this preloader. In the past we had to wire up our own tools of choice for this—which you can still do of course if you have other preferences. What is really nice and thoughtful is that it will restart automatically if you change some gems, initializers of config files—a nice and handy touch!

By default it is configured to run `rails` and `rake` commands only. So we need to set it up to also run with the `rspec` command we use for our running our tests. You can ask for the status of spring like so:

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

So, we get Spring out of the box to speed things up in Rails but we must not forget to add this little Gem to let Spring how to play ball with RSpec.

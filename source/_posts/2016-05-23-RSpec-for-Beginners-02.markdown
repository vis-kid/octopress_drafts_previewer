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

+ Generators

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



## Final Thoughts






These sorts of helpers in RSpec, not surprisingly as everywhere else, help you to encapsulate and therefore reuse arbitrary methods that you will maybe need all over the place. `/helpers` is a nice home for this sort of stuff and keeps your spec files cleaner and more DRY. The other thing such helpers support is readability. When you don’t have to focus on repeated implementation details of some frequently ocurring setup or whatever, you can package them up into a nicely readable helper method and store it in a single “authoritive” place among other helpers. That let’s you and other focus about the details of the actual scenario under test and get’s non-relevant stuff out of the way. Be careful not to hide important implementation details that are necessary to understand each test. Too clever abstractions that invite so-called mystery guests for example can bite you in the long run. 

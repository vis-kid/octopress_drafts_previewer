---
layout: post
title: AntiPatterns Basics—Rails Models
date: 2015-12-13 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Models/wheelchair-construction-fail.jpg %}

### Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to “classify” solutions that prevented them from reinventing the wheel all the time. It’s important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers. If this is rather new to you and you see yourself as being more on the beginner side of all things Ruby / Rails, then this one is exactly written for you. I think it’s best if you think of it as a quick skinny-dip into a much deeper topic whose mastery will not happen overnight.Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices and tools frameworks provide for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

### Topics

+ Fat Models
+ Missing Test Suite
+ Voyeuristic Models
+ Law of Demeter
+ Spaghetti SQL

+ ### Fat Models

I’m sure you have heard the “Fat models, skinny controllers” sing-song tons of times when you first started out with Rails. OK, now forget that! Sure, the business logic needs to be solved in the model layer but you shouldn’t feel inclined to stuff everything in there senselessly just to avoid crossing the lines into controller territory. Here’s a new target you should aim for: “Skinny models, skinny controllers”. You might ask, “well, how should we arrange code to achieve that—after all it’s a zero-sum game?” Good point! The name of the game is composition and Ruby is well equipped to give you lots of options to avoid model obesity.  

In most (Rails) web applications that are database backed, the majority of your attention and work will be centered around the model layer—given that you work with compentent designers who are able to implement their own stuff in the view I mean. Inherently your models will have more “gravity” and attract more complexity. The questions is just how you intend to manage that complexity. Active Record definitely gives you plenty of rope to hang yourself while making your lives incredibly easy. It is a tempting approach to design your model layer by just following the path of highest immediate convenience. Nevertheless, a future proof architecture takes a lot more consideration than cultivating huge classes and stuffing everything into Active Record objects.

The real problem that you deal with here is complexity—unnecessarily so I’d say. Classes that amass tons of code become complex just by their size alone. They are more difficult to maintain, difficult to parse and understand as well as increasingly harder to change because their composition probably lacks decoupling. These models often exceed their recommended capacity of handling one single responsibility and are rather all over the place. Worst case they become like garbage trucks, handling every trash that is lazily thrown at them. We can do better! If you think complexity is not a big deal—after all you are special, smart and all—think again! Complexity is the most notorious serial project killer out there—not your friendly neighborhood “Dark Defender”.

“Skinnier models” achieve one thing advanced folks in the coding business (probably a lot more professions than code and design) appreciate and what we all should absolutely strive for—simplicity! Or at least more of it which is a fair compromise if complexity is hard to eradicate. What tools does Ruby offer to make our lives easier in that regard and let’s us trim the fat out of our models? Simple, other classes and modules. You identify coherent code that you could extract into another object and thereby build a model layer that consists of reasonably sized agents that have their own unique, distinctive responsibilities. Think about it in terms of a talented performer. In real life, such a person might be able to rap, break, write lyrics and produce her own tunes. In programming, you prefer the dynamics of a band—here with at least four distinctive members—where each person is in charge of as few things as possible. You wanna build an orchestra of classes that can handle the complexity of the composer—not a micromanaging genius maestro class of all trades.

{% img /images/AntiPatterns/Models/genuis-maestro.png %}

Let’s look at an example of a fat model and play with a couple of options to handle its obesity. The example is a dummy one of course and by telling this goofy little story I hope it will be easier to digest and follow for newbies. We have a Spectre class that has too many responsibilities and has therefore grown unnecessarily. Besides these methods, I think it’s easy to imagine that such a specimen already accumulated lots of other stuff as well—represented by the three little dots. Spectre is well under way to become a [god class](https://robots.thoughtbot.com/how-much-should-i-refactor). (Chances are pretty low to sensibly formulate such a sentence again anytime soon;)

``` ruby
class Spectre < ActiveRecord::Base
  has_many :spectre_members
  has_many :spectre_agents
  has_many :enemy_agents
  has_many :operations

  ...

  def turn_mi6_agent(enemy_agent)
    puts "MI6 agent #{enemy_agent.name} turned over to Spectre"
  end

  def turn_cia_agent(enemy_agent)
    puts "CIA agent #{enemy_agent.name} turned over to Spectre"
  end

  def turn_mossad_agent(enemy_agent)
    puts "Mossad agent #{enemy_agent.name} turned over to Spectre"
  end

  def kill_double_o_seven(spectre_agent)
    spectre_agent.kill_james_bond
  end

  def dispose_of_cabinet_member(number)
    spectre_member = SpectreMember.find_by_id(number)

    puts "A certain culprit has failed the absolute integrity of this fraternity. The appropriate act is to smoke number #{number} in his chair. His services won’t be greatly missed"
    spectre_member.die
  end

  def print_assignment(operation)
    puts "Operation #{operation.name}’s objective is to #{operation.objective}."
  end

  private

  def enemy_agent
    #clever code
  end

  def spectre_agent
    #clever code
  end

  def operation
    #clever code
  end

  ...

end

```

Spectre turns various kinds of enemy agents, delegates killing 007, grills Spectre’s cabinet member when they fail and also prints out operational assignments. A clear case of micromanagement and definitely a violation of the “Single Responsibility Principle”. Private methods are also stashing up fast. This class doesn’t need to know most of the stuff that’s currently in it. We will split this functionality into a couple of classes and see if the complexity of having a couple more classes/objects is worth the liposuction.

``` ruby

class Spectre < ActiveRecord::Base
  has_many :spectre_members
  has_many :spectre_agents
  has_many :enemy_agents
  has_many :operations

  ...

  def turn_enemy_agent
    Interrogator.new(enemy_agent).turn
  end

  private 

  def enemy_agent
    self.enemy_agents.last
  end
end

class Interrogator
  attr_reader :enemy_agent
  
  def initialize(enemy_agent)
    @enemy_agent = enemy_agent
  end

  def turn
    enemy_agent.turn
  end
end

class EnemyAgent < ActiveRecord::Base
  belongs_to :spectre
  belongs_to :agency

  def turn
    puts 'After extensive brainwashing, torture and hoards of cash…'
  end
end

class MI6Agent < EnemyAgent
  def turn
    super
    puts "MI6 agent #{name} turned over to Spectre"
  end
end

class CiaAgent < EnemyAgent
  def turn
    super
    puts "CIA agent #{name} turned over to Spectre"
  end
end

class MossadAgent < EnemyAgent
  def turn
    super
    puts "Mossad agent #{name} turned over to Spectre"
  end
end

class NumberOne < ActiveRecord::Base
  def dispose_of_cabinet_member(number)
    spectre_member = SpectreMember.find_by_id(number)

    puts "A certain culprit has failed the absolute integrity of this fraternity. The appropriate act is to smoke number #{number} in his chair. His services won’t be greatly missed"
    spectre_member.die
  end
end

class Operation < ActiveRecord::Base
  has_many :spectre_agents
  belongs_to :spectre

  def print_assignment
    puts "Operation #{name}’s objective is to #{objective}."
  end
end

class SpectreAgent < ActiveRecord::Base
  belongs_to :operation
  belongs_to :spectre

  def kill_james_bond
    puts "Mr. Bond, I expect you to die!"
  end
end

class SpectreMember < ActiveRecord::Base
  belongs_to :spectre
  
  def die
    puts "Nooo, nooo, it wasn’t meeeeeeeee! ZCHUNK!"
  end
end

```

I think the most important part that you should pay attention to is how we used a plain Ruby class like `Interrogator` to handle the turning of agents from different agencies. Real world examples could represent a converter that, say, transforms a HTML document into pdf and vice versa. If you don’t need the full functionality of Active Record classes, why use them if a simple Ruby class can do the trick as well? A little less rope to hang ourselves. 

The Spectre class leaves the nasty business of turning agents to the `Interrogator` class and just delegates to it. This one has now the single responsibility of torturing and brainwashing captured agents. So far so good. But why did we create separate classes for each agent? Simple. Instead of just directly extracting the various turn methods like ```turn_mi6_agent``` over to `Interrogator` we gave them a better home in their own respective class. As a result, we can make effective use of polymorphism and don’t care about individual cases for turning agents. We just tell these different agent objects to turn and each of them knows what to do. The `Interrogator` doesn’t need to know the specifics about how each agent turns.

Since all these agents are Active Record objects, we created a generic one, `EnemyAgent`, that has a general sense of what turning an agent means and we encapsulate that bit for all agents in one place by subclassing it. We make use of this inheritance by supplying the `turn` methods of the various agents with `super` and get therefore access to the brainwashing and torture business—without duplication. Single responsibilities and no duplication is a good starting point for moving on.

The other Active Record classes take on various responsibilities that Spectre doesn’t need to care about. “Number One” usually does the grilling of failed Spectre cabinet members himself so why not let a dedicated object handle electrocution. On the other hand, failing Spectre members know how to die themselves when being smoked in their chair by `NumberOne`. `Operation` now prints its assignments itself as well—no need to waste the time of Spectre with peanuts like that. Last but not least, killing James Bond is usually attempted by an agent in the field, so ```kill_james_bond``` is now a method on `SpectreAgent`. Goldfinger would have handled that differently of course—gotta play with that laser thingie if you have one I guess.

As you can clearly see, we basically have now ten classes where we previously had only one. Isn’t that too much? It can be for sure. It’s an issue you’ll need to wrestle with most of the time you when split up such responsibilities. You can definitely overdue this. But looking at this from anther angle might help: 

+ Have we separated concerns? Absolutely!

+ Do we have lightweight, skinny classes that are better suited to handle singular responsibilities. Pretty sure!

+ Do we tell a “story”, are we painting a clearer picture of who is involved and is in charge for certain actions? I hope so!

+ Is it easier to digest what each class is doing? For sure!

+ Have we cut down on the number of private methods? Yup!

+ Does this represent a better quality of object oriented programming? Since we used composition and referred to inheritance only where needed for setting up these objects, you bet!

+ Does it feel more clean? Yes!
 
+ Are we better equipped to change our code without making a mess? Sure thing!

+ Was it worth it? What do you think?

I’m not implying that these questions need to be checked off your list every time but these are the things you probably should start asking yourself while slimming down your models. Designing skinny models can be hard but it’s an essential measure to keep your applications healthy and agile. These are also not the only constructive ways to deal with fat models but it’s a good start, especially for newbies.

+ ### Missing Test Suite 

That is probably the most obvious AntiPattern. Coming from the test-driven side of things, touching a mature app that has no test coverage can be one of the most painful experiences to encounter. If you wanna hate the world and your own profession more than anything, just spend six months on such a project and you’ll learn how much of a misanthrope is potentially in you. Kidding of course, but I doubt it will make you happier and that you wanna do it again—ever. Maybe a week will do as well. I’m pretty sure, the word torture will pop into your mind more often than you think. If testing was not part of your process so far and that kinda pain feels normal to your work, maybe you should consider that testing is not that bad nor your enemy. When your code related joy levels are more or less constantly above zero and you can fearlessly change your code then the overall quality of your work will be a lot higher compared to output that is tainted by anxiety and suffering. 

Am I overestimating? I really don’t think so! You want to have a very extensive test coverage, not only because it is a great design tool for only writing code that you actually need but also you will need to change your code at some point in the future. You will be a lot better equipped to engage with your codebase—and a lot more confident—if you have a test harness that aides and guides refactorings, maintenance and extensions. They will occur for sure down the road, zero doubts about that. This is also the point where a test suite starts to pay off the second round of dividends because the increased speed with which you can securely make these quality changes can not be achieved by a long shot in apps that are made by people who think writing tests is nonsense or cost too much time. 

+ ### Voyeuristic Models

These are models that are super nosy and want to gather too much information about other objects / models. That is in stark contrast to one of the most fundamental ideas in Object Oriented Programming—encapsulation. We rather want to strive for self-contained classes / models that manage their internal affairs themselves as much as possible. In terms of programming concepts, these voyeuristic models basically violate the “Principle of Least Knowledge”, aka the “Law of Demeter”—however you wanna pronounce it. 

### Law of Demeter

Why is this a problem? It is a form of duplication—a subtle one—and also leads to code that is a lot more brittle than anticipated. The Law of Demeter is pretty much the most reliable code smell that you can always attack without being worried about the possible downsides. I guess calling this one a “law” was not as pretentious as it might sound at first. Dig into this smell, you will need it a lot in your projects. It basically states that in terms of objects, you can call methods on your objects friend but not on your friend’s friend. This is a common way to explain it and it all boils down to using not more than a single dot for your method calls. Btw, it is totally fine to use more dots or methods calls when you deal with a single object that does not try to reach further than that. Something like `@weapons.find_by_name('Poison dart').formula`. is just fine. Finders can pile up quite a few dots sometimes. Btw, encapsulating them in dedicated methods is nevertheless a good idea.

#### Law of Demeter violations

Let’s look at a couple of bad examples from the classes above:

``` ruby

@operation.spectre_agents.first.kill_james_bond

@spectre.operations.last.spectre_agents.first.name

@spectre.enemy_agents.last.agency.name

```

To get the hang of it, here are a few more fictional ones.

``` ruby

@quartermaster.gizmos.non_lethal.favorite

@mi6.operation.agent.favorite_weapon

@mission.agent.name

```

Bananas, right? Doesn’t look good, does it? As you can see, these method calls peek too much into the business of other objects. The most important and obvious negative consequence is changing a bunch of these method calls all over the place if the structure of these objects need to change—which they will eventually because the only constant in software development is—you guessed it—change (maybe feeling stupid every now and then as well but I’m not so sure about this one). Also, it looks really nasty, not easy on the eyes at all. When you don’t know that this is a problematic approach, Rails let’s you take this very far anyway—without screaming at you. A lot of rope, remember?

So what can we do about this? After all we want to get that information somehow. On the one hand you can compose our objects to fit our needs and we can make clever use of delegation to keep our models slim at the same time. Let’s dive into some code to show you what I mean.

``` ruby

class SpectreMember < ActiveRecord::Base
  has_many :operations
  has_many :spectre_agents
  
  ...

end

class Operation < ActiveRecord::Base
  belongs_to :spectre_member

  ...

end

class SpectreAgent < ActiveRecord::Base
  belongs_to :spectre_member

  ...

end

@spectre_member.spectre_agents.all
@spectre_member.operations.last.print_assignment
@spectre_member.spectre_agents.find_by_id(1).name

@operation.spectre_member.name
@operation.spectre_member.number
@operation.spectre_member.spectre_agents.first.name

@spectre_agent.spectre_member.number

```

``` ruby

class SpectreMember < ActiveRecord::Base
  has_many :operations
  has_many :spectre_agents
  
  ...

  def list_of_agents
    spectre_agents.all
  end

  def print_operation_details
    operation = Operation.last
    operation.print_operation_details
  end
end

class Operation < ActiveRecord::Base
  belongs_to :spectre_member

  ...

  def spectre_member_name
    spectre_member.name
  end

  def spectre_member_number
    spectre_member.number
  end

  def print_operation_details
    puts "This operation’s objective is #{objective}. The target is #{target}"
  end
end

class SpectreAgent < ActiveRecord::Base
  belongs_to :spectre_member

  ...

  def superior_in_charge
    puts "My boss is number #{spectre_member.number}"
  end
end

@spectre_member.list_of_agents
@spectre_member.print_operation_details

@operation.spectre_member_name
@operation.spectre_member_number

@spectre_agent.superior_in_charge

```

This is definitely a step in the right direction. As you can see, we packed the info we wanted to acquire in a bunch of wrapper methods. Instead of reaching across many objects directly, we abstracted these bridges and leave it to the respective models to talk to their friends about the infos we need. The downside to this approach is having all these extra wrapper methods lying around. Sometimes it’s fine but we really want to avoid maintaining these methods in a bunch of places if an object changes.

If possible, the dedicated place for them to change is on their object—and on their object alone. Polluting objects with methods that have little to do with their own model itself is also something to look out for since this is always a potential hazard for watering down on single responsibilities. We can do better than that. Where possible, let’s delegate method calls directly to their objects in charge and try to cut down on wrapper methods as much as we can. Rails knows what we need and provides us with the handy `delegate` class method method to tell our object’s friends what methods we need called. 

Let’s zoom in on something from the previous code example and see where we can make proper use of delegation.

``` ruby

class Operation < ActiveRecord::Base
  belongs_to :spectre_member
 
  delegate :name, :number, to: :spectre_member, prefix: true

  ...

# def spectre_member_name
#   spectre_member.name
# end

# def spectre_member_number
#   spectre_member.number
# end

  ...

end

@operation.spectre_member_name
@operation.spectre_member_number



class SpectreAgent < ActiveRecord::Base
  belongs_to :spectre_member

  delegate :number, to: :spectre_member, prefix: true

  ...

  def superior_in_charge
    puts "My boss is number #{spectre_member_number}"
  end

  ...

end

```

As you can see we could simplify things a bit using method delegation. We got rid of ```Operation#spectre_member_name``` and ```Operation#spectre_member_number``` completely and `SpectreAgent` does not need call `number` on ```spectre_member``` anymore—`number` is delegated back directly to its “origin” class `SpectreMember`. 

In case this is a little confusing at first, how does this work exactly? You tell delegate which ```:method_name``` it should delegate `to:` which ```:class_name``` (multiple method names are fine too). The `prefix: true` part is optional. In our case, it prefixed the snake-cased class name of the receiving class before the method name and enabled us to call ```operation.spectre_member_name``` instead of the potentially ambiguous `operation.name`—if we had not used the prefix option. This works really nice with ```belongs_to``` and ```has_one``` associations. On the ```has_many``` side of things the music will stop and you will run into trouble though. These associations provide you with a collection proxy that will throw NameErrors or NoMethodErros at you when you delegate methods to these “collections”.

+ ### Spaghetti SQL

To round off this chapter about model AntiPatterns in Rails I’d like to spend a little time on what to avoid when SQL is involved. Active Record associations provide options that make your lives substantially easier when you are aware what you should stay away from. Finder methods are a whole topic on their own—and we won’t cover them in their full depth—but I wanted to mention a few common techniques that help you even when you write very simple ones.

Things that we should be concerned about echo most of what we learned so far. We want to have intention-revealing, simple and reasonably named methods for finding stuff in our models. Let’s dive right into code. 

``` ruby

class Operation < ActiveRecord::Base

  has_many :agents

  ...

end

class Agent < ActiveRecord::Base

  belongs_to :operation

  ...

end

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @agents = Agent.where(operation_id: @operation.id, licence_to_kill: true)
  end
end

```

Looks harmless, no? We’re juuust looking for a bunch of agents that have the licence to kill for our ops page. Think again. Why should the `OperationsController` dig into the internals of `Agent`? Also, is this really the best we can do to encapsulate a finder on `Agent`? If you are thinking that you could add a class method like `Agent.find_licence_to_kill_agents` which encapsulates the finder logic you are definitely doing a step in the right direction—not nearly enough though. 

``` ruby

class Agent < ActiveRecord::Base

  belongs_to :operation

  def self.find_licence_to_kill_agents(operation)
    where(operation_id: operation.id, licence_to_kill: true)
  end
  ...

end

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @agents = Agent.find_licence_to_kill_agents(@operation)
  end
end

```

We have to be a bit more engaged than that. First of all, this is not using the associations to our advantage and encapsulation is also suboptimal. Associations like `has_many` come with the benefit that we can add on to the proxy array that we get returned. We could have done this instead:

``` ruby

class Operation < ActiveRecord::Base

  has_many :agents

  def find_licence_to_kill_agents
    self.agents.where(licence_to_kill: true)
  end
  ...

end

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @agents = @operation.find_licence_to_kill_agents
  end
end

```

This works for sure but is also just another small step in the right direction. Yes, the controller is a bit better and we make good use of model associations but you should still be suspicious why `Operation` is concerned about the implementation of finding a certain type of `Agent`. This responsibility belongs back to the `Agent` model itself. Named scopes come in pretty handy with that. Scopes define chainable—very important—class methods for your models and thereby allow you the specify useful queries which you can use as additional method calls on top of your model associations. The following two approaches for scoping `Agent` are indifferent.

``` ruby

class Agent < ActiveRecord::Base
  belongs_to :operation

  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
end

class Agent < ActiveRecord::Base
  belongs_to :operation

  def self.licenced_to_kill
    where(licence_to_kill: true)
  end
end

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @agents = @operation.agents.licenced_to_kill
  end
end

``` 

That is much better. In case the syntax of scopes is new to you, they are just (stabby) lambdas—not terribly important to look into them right away btw—and they are the proper way to call scopes since Rails 4. `Agent` is now in charge of managing its own search parameters and associations can just tuck on what they need to find. This approach let’s you achieve queries as single SQL calls. I personally like to use `scope` for its explicity and they are very handy to chain inside well-named finder methods—that way they increase the possiblity of reusing code and DRY-ing code. Let’s say we have something a bit more involved:

``` ruby

class Agent < ActiveRecord::Base
  belongs_to :operation

  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
  scope :womanizer, -> { where(womanizer: true) }
  scope :bond, -> { where(name: 'James Bond') }
  scope :gambler, -> { where(gambler: true) }
end

```

We can now use all these scopes to custom build more comples queries.

``` ruby

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @double_o_agents = @operation.agents.licenced_to_kill
  end

  def show
    @operation = Operation.find(params[:id])
    @bond = @operation.agents.womanizer.gambler.licenced_to_kill
  end

  ...
end

```

Sure that works but I’d like to suggest you go one step further.

``` ruby

class Agent < ActiveRecord::Base
  belongs_to :operation

  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
  scope :womanizer, -> { where(womanizer: true) }
  scope :bond, -> { where(name: 'James Bond') }
  scope :gambler, -> { where(gambler: true) }

  def self.find_licenced_to_kill
    licenced_to_kill
  end

  def self.find_licenced_to_kill_womanizer
    womanizer.licenced_to_kill
  end

  def self.find_gambling_womanizer
    gambler.womanizer
  end
 
  ...

end

class OperationsController < ApplicationController

  def index
    @operation = Operation.find(params[:id])
    @double_o_agents = @operation.agents.find_licenced_to_kill
  end

  def show
    @operation = Operation.find(params[:id])
    @bond = @operation.agents.find_licenced_to_kill_womanizer
    #or
    @bond = @operation.agents.bond
  end

  ...

end

```

As you can see, through this approach we reap the benefits of proper encapsulation, model associations, code reuse and expressive naming of methods—and all while doing single SQL queries. No more spaghetti code, awesome! If you are worried about violating the Law of Demeter thingie you will be pleased to hear that since we are not adding dots by reaching into the associated model but chaining them only onto their own object we are not commiting any demeter crimes.

+ ### Final thoughts

From a beginner’s perspective I think you have learned a lot about better handling of Rails Models and how to model them more robustly without calling for a hangman. Don’t be fooled though in thinking that there isn’t a lot more to learn on this particular topic. I presented you with a few AntiPatterns that I think newbies are able to easily understand and handle in order to protect themselves early on. If you don’t know what you don’t know, plenty of rope is available for looping around your neck. Although this was a solid start into this topic, there are not only more aspects to AntiPatterns in Rails models but also more nuances which you’ll need to explore as well. These were the basics—very essential and important ones—and you should feel accomplished for a little while that you haven’t waited until much later in your career to figure them out.

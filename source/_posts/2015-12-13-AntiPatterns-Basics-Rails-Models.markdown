---
layout: post
title: AntiPatterns Basics—Rails Models
date: 2015-12-13 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/antipatterns-models/wheelchair-construction-fail.jpg %}

### Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s lifecycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

### Topics

+ Fat Models
+ Missing Test Suite
+ Voyeuristic Models
+ Law of Demeter
+ Spaghetti SQL

+ ### Fat Models

I’m sure you have heard the “Fat models, skinny controllers” sing-song tons of times when you first started out with Rails. Ok, now forget that! Sure, the business logic needs to be solved in the model layer but you shouldn’t feel inclined to stuff everything in there senselessly just to avoid crossing the lines into controller territory. Here’s a new target you should aim for: “Skinny models, skinny controllers”. You might ask, “well, how should we arrange code to achieve that—after all it’s a zero-sum game?” Good point! The name of the game is composition and Ruby is well equiped to give you lots of options to avoid model obesity.  

In most (Rails) web applications that are database backed, the majority of your attention and work will be centered around the model layer. Naturally, nothing wrong with that. Inherently your models will have more gravity and attract more complexity. The questions is just how you intend to manage that complexity. Active Record definitely gives you plenty of rope to hang yourself while making your lives incredibly easy. It is a tempting approach to design your model layer by just following the path of hightest immediate convenience. Nevertheless, a future proof architecture takes a lot more consideration than cultivating huge classes and stuffing everything into Active Record objects.

The real problem that you deal with here is complexity—unnecessarily so I’d say. Classes that amass tons of code beome complex just by their size alone. They are more difficult to maintain, difficult to parse and understand as well as increasingly harder to change because their composition probably lacks decoupling. These models often exceed their recommended capacity of handling one single repsonsibility and are rather all over the place. Worst case they become like garbage trucks, handling every trash that is lazily thrown at them. We can do better! If you think complexity is not a big deal—after all you are special, smart and all—think again! Complexity is the most notorious serial project killer out there—not your friendly neighborhood “Dark Defender”.

“Skinnier models” achieve one thing advanced folks in the coding business (proably a lot more professions than code and design) appreciate and what we all should absolutely strive for—simplicity! Or at least more of it which is a fair compromise if complexity is hard to erradicate. What tools does Ruby offer to make our lives easier in that regard and let’s us trim the fat out of our models? Simple, other classes and modules. You identify coherent code that you could extract into another object and thereby build a model layer that consists of reasonably sized agents that have their own unique, distinctive responsibilities. Think about it in terms of a talented performer. In real life, such a person might be able to rap, break, write lyrics and produce her own tunes. In programming, you prefer the dynamics of a band—here with at least four distinctive members—where each person is in charge of as few things as possible. 

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

  def kill_james_bond(spectre_agent)
    #clever code
  end

  def assign_new_operation(spectre_agent)
    #clever code
  end

  def dispose_of_cabinet_member(spectre_member)
    puts "A certain culprit has failed the absolute integrity of this fraternity. The appropriate act is to smoke number #{spectre_member.number} in his chair. His services won’t be greatly missed"
  end

  def print_assignment(operation)
    #clever code
  end
end
```

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
    puts 'Agent is turning'
    agency = "Spectre"
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
    spectre_member = SpectreMember.new(number)
    puts "A certain culprit has failed the absolute integrity of this fraternity. The appropriate act is to smoke number #{spectre_member} in his chair. His services won’t be greatly missed"
  end
end

class SpectreAgent < ActiveRecord::Base
  belongs_to :spectre

  def kill_james_bond
    #clever code
  end

  def assign_new_operation(operation)
    #clever code
  end
end

class SpectreMember < ActiveRecord::Base
  belongs_to :spectre
end

class Operation < ActiveRecord::Base
  belongs_to :spectre

  def print_assignment
    #clever code
  end
end

```

+ ### Missing Test Suite 

That is probably the most obvious AntiPattern. Coming from the test-driven side of things, touching a mature app that has not test coverage can be one of the most painful experiences you can encounter. If you wanna hate the world and your own profession more than anything, just spend six months on such a project and you’ll learn how much of a misanthrope is potentially in you. Kidding of course, but I doubt it will make you happier and that you wanna do it again—ever. Maybe a week will do as well. I’m pretty sure, the word torture will pop into your mind more often than you think. If testing was not part of your process so far and that kinda pain feels normal to your work, maybe you should consider that testing is not that bad nor your enemy. When your code related joy levels are more or less constanly above zero and you can fearlessly change your code then the overall quality of your work will be a lot higher compared to output that is tainted by anxiety and suffering. 

Am I overestimating? I really don’t think so! You want to have a very extensive test coverage, not only because it is a great design tool for only writing code that you actually need but also you will need to change your code at some point in the future. You will be a lot better equipped to engage with your codebase—and a lot more confident—if you have a test harness that aides and guides refactorings, maintenance and extensions. They will occur for sure down the road, zero doubts about that. This is also the point where a test suite starts to pay off the second round of dividends because the increased speed with which you can securely make these quality changes can not be achieved by a long shot in apps are made by people who think writing tests is nonsense or cost too much time. 

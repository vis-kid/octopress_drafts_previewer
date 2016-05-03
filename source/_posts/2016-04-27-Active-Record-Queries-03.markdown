---
layout: post
title: Queries in Rails 03
date: 2016-04-27 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, Active Record, Queries, Ruby, Ruby on Rails]
---

{% img /images/Rails-Queries/holmes-ruby.png 400 %}

## Topics

+ Scopes & Associations
+ Slimmer Joins
+ Merge
+ has_many
+ Custom Joins

In this last piece we are going to look a bit deeper into queries and try to play with a few more advanced scenarios. We will cover relationships of Active Record models a bit more in this article but I try to stay away from examples that could be too confusing for programming newbies. Before you move forward, stuff like the example below should not cause any confusion:

###### Rails

``` ruby

Mission.last.agents.where(name: 'James Bond')

```

 If you are new to Active Record queries and SQL, I recommend that you take a look at my previous two articles before you continue. This one might be hard to swallow without the knowledge that I was building up so far. Up to you of course. On the flip side, this article is not gonna be as long as the other ones if you just wanna look at these slightly advanced use cases. Let’s dig in! 

## Scopes & Associations

Let’s reiterate. We can query Active Record models right away, but assocications are also fair game for queries—and we can chain all these thingies. So far so good. We can pack finders into neat, reuseable scopes in your models as well and I briefly mentioned their similarity to class methods.

###### Rails

``` ruby

class Agent < ActiveRecord::Base

  belongs_to :mission

  scope :find_bond,        -> { where(name: 'James Bond') }
  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
  scope :womanizer,        -> { where(womanizer: true) }
  scope :gambler,          -> { where(gambler: true) } 
end

# => Agent.find_bond
# => Agent.licenced_to_kill
# => Agent.womanizer
# => Agent.gambler

# => Mission.last.agents.find_bond
# => Mission.last.agents.licenced_to_kill
# => Mission.last.agents.womanizer
# => Mission.last.agents.gambler

# => Agent.licenced_to_kill.womanizer.gambler
# => Mission.last.agents.womanizer.gambler.licenced_to_kill

```

So you can also pack them into your own class methods and be done with it. Scopes are not iffy or anything I think—although people mention them as being a bit magic here and here—, but since class methods achieve the same thing, I’d opt for that.

###### Rails

``` ruby

class Agent < ActiveRecord::Base

  belongs_to :mission

  def self.find_bond
    where(name: 'James Bond')
  end

  def self.licenced_to_kill
    where(licence_to_kill: true)
  end

  def self.womanizer
    where(womanizer: true)
  end

  def self.gambler
    where(gambler: true)
  end
end

# => Agent.find_bond
# => Agent.licenced_to_kill
# => Agent.womanizer
# => Agent.gambler

# => Mission.last.agents.find_bond
# => Mission.last.agents.licenced_to_kill
# => Mission.last.agents.womanizer
# => Mission.last.agents.gambler

# => Agent.licenced_to_kill.womanizer.gambler
# => Mission.last.agents.womanizer.gambler.licenced_to_kill

```

These class methods read just the same and you don’t need to stab anybody with a lambda. Whatever works best for your or your team—up to you what API you wanna use. Just don’t mix and match them, stick with once choice! Both versions let you easily chain these methods inside another class method for example:

###### Rails

``` ruby

class Agent < ActiveRecord::Base

  belongs_to :mission

  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
  scope :womanizer,        -> { where(womanizer: true) }

  def self.find_licenced_to_kill_womanizer
    womanizer.licenced_to_kill
  end
end

# => Agent.find_licenced_to_kill_womanizer

# => Mission.last.agents.find_licenced_to_kill_womanizer

```

###### Rails

``` ruby

class Agent < ActiveRecord::Base

  belongs_to :mission

  def self.licenced_to_kill
    where(licence_to_kill: true)
  end

  def self.womanizer
    where(womanizer: true)
  end

  def self.find_licenced_to_kill_womanizer
    womanizer.licenced_to_kill
  end
end

# => Agent.find_licenced_to_kill_womanizer

# => Mission.last.agents.find_licenced_to_kill_womanizer

```

Let’s take this one little step further—stay with me. We can use a lambda in associations themselves to define a certain scope. Looks a bit weird at first but they can be pretty handy. That makes it possible to call these lambdas right on your associations. Pretty cool and highly readable with shorter methods chaining going on. Be aware of coupling these models too tight though.

###### Rails

``` ruby

class Mission < ActiveRecord::Base

  has_many :double_o_agents,
    -> { where(licence_to_kill: true) },
    class_name: "Agent"

end

# => Mission.double_o_agents

```

Tell me this isn’t cool somehow! Not for everyday use but dope to have I guess. So here we `Mission` can “request” only agents that have the licence to kill. A word about the syntax, since we strayed from naming conventions and used something more expressive like ```double_o_agents```. We need to mention the class name to not confuse Rails which might otherwise expect to look for a class `DoubleOAgent`. You can of course have both `Agent` associtaions in place—the usual and your custom one—and Rails won’t complain.

###### Rails

``` ruby

class Mission < ActiveRecord::Base

  has_many :agents

  has_many :double_o__agents,
    -> { where(licence_to_kill: true) },
    class_name: "Agent"

end

# => Mission.agents
# => Mission.double_o_agents

```

## Slimmer Joins

When you query the database for records and you need not all of the data, you can choose to specify what exactly you want returned. Why? Because the data returned to Active Record will eventually be built into new Ruby Objects. Let’s look at one simple strategy to avoid memory bloat in your Rails app:

###### Rails

``` ruby

class Mission < ActiveRecord::Base
  has_many :agents
end

class Agent < ActiveRecord::Base
  belongs_to :mission
end

```

###### Rails

``` ruby

Agent.all.joins(:mission)

```

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id"

```

So this query returns a list of agents with a mission from the database to Active Record—which then in turn sets out to build Ruby objects out of it. The `mission` data is available since the data from these rows are joined onto the rows of the agent’s data. That means, the joined data is available during the query but will not get returned to Active Record. So you will have this data to perfom calculations for example. It’s especially cool because you can make use of data that does also not get sent back to your app. Less attributes that need to get built into Ruby objects—that take up memory—can be a big win. In general, think about sending only the absolute necessary rows and columns back that you need. That way you can avoid bloat quite a bit.

###### Rails

``` ruby

Agent.all.joins(:mission).where(missions: { objective: "Saving the world" })

```

Just a quick aside about the syntax here, because we are not querying the `Agent` table via `where` here, but the joined `:mission` table, we need to specify that we are looking for specific `missions` in our `WHERE` clause.

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."objective" = ?  [["objective", "Saving the world"]]

```

Just a reminder, using `includes` here would also return missions to Active Record for eager loading and take up memory building Ruby objects.

## Merge

A `merge` comes in handy for example when we want to combine a query on agents and their associated missions that have a specific scope that is defined by you. We can take two ActiveRecord::Relation objects and can merge their conditions. Sure, no biggie but `merge` is useful if you want to use a certain scope while using an association. In other words, what we can do with `merge` is filter by a named scope on the joined model. In one of the previous examples we used class methods to define such named scopes ourselves.

###### Rails

``` ruby

class Mission < ActiveRecord::Base
  has_many :agents

  def self.dangerous
    where(enemy: "Ernst Stavro Blofeld")
  end
end

class Agent < ActiveRecord::Base
  belongs_to :mission
end

```

###### Rails

``` ruby

Agent.joins(:mission).merge(Mission.dangerous)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

When we encapsulate what a ```dangerous``` mission is within the `Mission` model, we can tuck it on a `join` via `merge` this way. So moving the logic of such conditions to the relevant model where it belongs is on the one side a nice technique to achieve looser coupling—we don’t want our Active Record models to know a lot of details about each other—and on the other hand, it gives you a nice APi in your joins without blowing up in your face. The example below without merge would not work without an error:

###### Rails

``` ruby

Agent.all.merge(Mission.dangerous)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

When we now merge on an ActiveRecord::Relation object for our missions onto our agents then the database doesn’t know which missions we are talking about. We need to make clear which association we need and join the mission data first—or SQL gets confused. One last cherry on top. We can encapsulate this even better by involving the agents as well: 

###### Rails

``` ruby

class Mission < ActiveRecord::Base
  has_many :agents

  def self.dangerous
    where(enemy: "Ernst Stavro Blofeld")
  end
end

class Agent < ActiveRecord::Base
  belongs_to :mission

  def self.double_o_engagements
    joins(:mission).merge(Mission.dangerous)
  end
end
```

###### Rails

``` ruby

Agent.double_o_engagements

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

That’s some sweet cherry in my book. Encapsulation, proper OOP and great readability. Jackpot!

## has_many

Above we have seen the ```belongs_to``` association in action a lot. Let’s take a look at this from another perspective and bring secret service sections into the mix:

###### Rails

``` ruby

class Section < ActiveRecord::Base
  has_many :agents
end

class Mission < ActiveRecord::Base
  has_many :agents
end

class Agent < ActiveRecord::Base
  belongs_to :mission
  belongs_to :section
end

```

So in this scenario, agents would not only have a ```mission_id``` but also a ```section_id```. So far so good. Let’s find all sections with agents with a specific mission—so sections that have some sort of mission going on.

###### Rails

``` ruby

Section.joins(:agents)

```

###### SQL

``` sql

SELECT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections."id"

```

Did you notice something? A small detail is different. The foreign keys are flipped. Here we are requesting a list of sections but use foreign keys like this: ```"agents"."section_id" = "sections."id"```. In other words, We are looking for a foreign key from a table we are joining.

###### Rails

``` ruby

Agent.joins(:mission)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id"

```

Previously our joins via a ```belongs_to``` association looked like this: the foreign keys were mirrored: ```"missions"."id" = "agents"."mission_id"``` and were looking for the foreign key from the table we are starting.

Going back to your ```has_many``` scenario, we now would get a list of sections that are repeated because they have multiple agents in each section of course. So for each agent column that gets joined on, we get a row for this section / section_id—in short, we are duplicating rows basically. To make this even more dizzying, let’s bring missions into the mix as well.

###### Rails


``` ruby

Section.joins(agents: :mission)

```

###### SQL

``` sql

SELECT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections"."id" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id"

```

Check out the two `INNER JOIN` parts. Still with me? We are kinda “reaching” through agents to their missions from the agent’s section. Yeah, stuff for fun headaches, I know. What we get are missions that indirectly associate with a ceratain section. As a result, we get new columns joined on but the number of rows is still the same that gets returned by this query. What gets sent back to Active Record—resulting in builing new Ruby objects—is also still the list of sections btw. So when we have multiple missions going on with multiple agents, we would get duplicated rows for our section again. Let’s filter this some more:

###### Rails

``` ruby

Section.joins(agents: :mission).where(missions: { enemy: "Ernst Stavro Blofeld" })

```

###### SQL

``` sql

SELECT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections"."id" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = 'Ernst Stavro Blofeld'

```

Now we only get sections returned that are involved in missions where Ernst Stavro Blofeld is the involved enemy. Kosmopolitan as some super villains might think of themselves, they could operate in more than one section—say section A and C, the United Sates and Canada respectively. If we have multiple agents in a given section who are working on the same mission to stop Blofeld or whatever, we would again have repeated rows returned to us into Active Record. Let’s be a bit more distinct about it:

###### Rails

``` ruby

Section.joins(agents: :mission).where(missions: { enemy: "Ernst Stavro Blofeld" }).distinct

```

###### SQL

``` sql

SELECT DISTINCT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections"."id" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = 'Ernst Stavro Blofeld'

```

What this gives us is the number of sections that Blowfeld tries operates out—that are known of—which has agents active in missions with him as an enemy. As a final step, let’s do some refactoring again. We extract this into a nice “little” class method on `class Section`:

###### Rails

``` ruby

class Section < ActiveRecord::Base
  has_many :agents

  def self.critical
    joins(agents: :mission).where(missions: { enemy: "Ernst Stavro Blofeld" }).distinct
  end
end

class Mission < ActiveRecord::Base
  has_many :agents
end

class Agent < ActiveRecord::Base
  belongs_to :mission
  belongs_to :section
end

```

You can refactor this even more and split up the reponsibilities to achieve looser coupling, but let’s move on for now.

## Custom Joins 

Most of the time you can rely on Active Record writing the SQL that you want for you. That means that you stay in Ruby land and don’t need to worry about database details all too much. But sometimes you need to poke a hole into SQL land and do your own thing. For example, if you need to use a `LEFT` join and break out of Active Record’s usual behaviour of doing an `INNER` join by default. `joins` is a little window to write your own custom SQL if needed. You open it, plug in your custom query code, close the “window” and can continue to add on Active Record query methods.

Let’s demonstrate this with an example that involves gadgets. Let’s say a typical agent usually ```has_many``` gadgets and we want to find agents that are not equipped with any fancy gadgetary to help them in the field. A usual join would not yield good results since we are actually interested in `nil`—or `null` in SQL speak—values of these spy toys.

###### Rails

``` ruby

class Mission < ActiveRecord::Base
  has_many :agents
end

class Agent < ActiveRecord::Base
  belongs_to :mission
  has_many   :gadgets
end

class Gadget < ActiveRecord::Base
  belongs_to :agent
end

```

When we do a `joins` operation we will get only agents returned that are already equipped with gadgets because the ```agent_id``` on these gadgets are not nil. This is the expected behaviour of a default inner join. The inner joins builds upon a match on both sides and returns only rows of data that matches this condition. A non-existent gadget with a `nil` value for an agent who carries no gadget does not match that criteria. 

###### Rails

``` ruby

Agent.joins(:gadgets)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "gadgets" ON "gadgets"."agent_id" = "agents"."id"

```

We are on the other hand looking for schmuck agents that are in dire need of some love from the quartermaster. Your first guess might have looked like the following:

###### Rails

``` ruby

Agent.joins(:gadgets).where(gadgets: {agent_id: nil})  

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "gadgets" ON "gadgets"."agent_id" = "agents"."id" WHERE "gadgets"."agent_id" IS NULL

```

Not bad, but as you can see from the SQL output, it does not play along and still insists on the default `INNER JOIN`. That’s a scenario where we need an `OUTER` join, because one side of our “equation” is missing so to speak. We are looking for results for gadgets that don’t exist—more precise, for agents without gadgets. So far, when we passed a symbol to Active Record in a join, it was expecting an association. With a string passed in on the other hand, it expects it to be an actual fragment of SQL code—a part of your query.

###### Rails

``` ruby

Agent.joins("LEFT OUTER JOIN gadgets ON gadgets.agent_id = agents.id").where(gadgets: {agent_id: nil})

```

###### SQL

```sql

SELECT "agents".* FROM "agents" LEFT OUTER JOIN gadgets ON gadgets.agent_id = agents.id WHERE "gadgets"."agent_id" IS NULL

```

Or, if you are curious about lazy agents without missions—hanging possibly in Barbodos or wherever—our custom join would look like this:

###### Rails

``` ruby

Agent.joins("LEFT OUTER JOIN missions ON missions.id = agents.mission_id").where(missions: { id: nil })  

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" LEFT OUTER JOIN missions ON missions.id = agents.mission_id WHERE "missions"."id" IS NULL

```

The outer join is the more inclusive join version since it will match all records from the joined tables, even if some of these relationships don’t exist yet. Because this approach is not as exclusive as inner joins, you will get a bunch of nils here and there. This can be informative in some cases of course but inner joins are nevetheless usually what we are looking for. Rails 5 will let us use a specialized method called `left_outer_joins` instead for such cases. Finally! One little thing for the road: keep these peeping holes into SQL land as small as possible if you can. You will do everybody—including your future self—a tremendous favor.

## Final Thoughts

Getting Active Record to write efficient SQL for you is one of the main skills you should take away from this mini-series for beginners. That way, you will also get code that is compliant with whatever database it supports—meaning that the queries will be stable across databases. It is necessary that you not only understand how to play with Active Record but the underlying SQL is of equal importance. Yes, SQL can be boring, tedious to read and not elegant looking, but don’t forget that Rails wraps Active Record around SQL and you should not neglect understanding this vital piece of technology—just because Rails makes it very easy to not care most of the time. Effeciency is crucial for database queries, especially if you build something for larger audiences with heavy traffic. Now go out on the internets and find some more material on SQL to get it out of your system—once and for all!

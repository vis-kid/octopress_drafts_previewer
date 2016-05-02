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

+ Associations & Scopes
+ Slimmer Joins
+ Merge

In this last piece we are going to look a bit deeper into queries and try to play with a few more advanced scenarios. If you are new to Active Record queries and SQL, I recommend that you take a look at my previous two articles before you continue. This one might be hard to swallow without the knowledge that I was building up so far. Up to you of course. On the flip side, this article is not gonna be as long as the other ones if you just wanna look at these advanced use cases. Let’s dig in! 

Writing some of the SQL and integrate it with your Active Record code. You should have now at least have a basic SQL knowledge and understand what Active Record does and what it offers in terms of queries. We will cover relationships of Active Record models a bit more in this article but I try to stay away from examples that could be too confusing for programming newbies. Before you move forward, stuff like the example below should not cause any confusion: 

###### Rails

``` ruby

Mission.last.agents.where(name: 'James Bond')

```

## Associations & Scopes

Let’s reiterate. We can query Active Record models but right away, assocications are also fair game for queries and we can chain all these thingies. So far so good. I briefly mentioned that we can pack them into neat and handy scopes in your models as well.

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

```


What you can also do is pack them into your own class methods and be done with it. Scopes are not iffy or anything I think—although people mention it as being a bit magic here and here—, but since class methods achieve the same thing, I’d opt for that.

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

```

These class methods read just the same and you don’t need to stab anybody with a lambda. Whatever works best for your you or your team, up to you what API you wanna use here. Just don’t mix and match them and stick with once choice!

Both versions let you easily chain these methods inside another class method for example:

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

Let’s take this one little step further—stay with me. We can use a lambda in associations itself to define a certain scope. Looks a bit weird at first but they can be pretty handy. That makes it possible to call these lambdas right on your associtaions. Pretty cool and highly readable with shorter methods chaining going on. Be aware of coupling these models too tight though.

###### Rails

``` ruby

class Mission < ActiveRecord::Base

  has_many :double_o__agents,
    -> { where(licence_to_kill: true) },
    class_name: "Agent"

end

# => Mission.double_o_agents

```

Tell me this isn’t cool somehow! Not for everyday use but cool to have I guess. A word about the syntax, since we strayed from naming conventions and used something more expressive using ```double_o_agents```, we need to mention the class name to not confuse Rails.

You can of course have both `Agent` associtaions in place—the usual and your custom one—and Rails won’t complain.

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

When you query the database for records and you need not all of the data, you can choose to specify what exactly you want returned. Why? Because the attributes returned to Active Record will eventually be built into new Ruby Objects. This would be one strategy to avoid memory bloat in your Rails app.
s

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

So this query returns a list of agents from the database to Active Record—which then in turn sets out to build Ruby objects out of it. The `mission` data is available since the data from these rows are joined onto the rows of the agent’s data. That means, the joined data is available during the query but will not get returned to Active Record. So you will have this data to perfom calculations for example. It’s especially cool because you can make use of data that does also not get sent back to your app. Less attributes that need to get built into Ruby objects—that take up memory—can be a big win. In general, think about sending only the absolute necessary rows and columns back that you need. That way you avoid bloat quite a bit.

###### Rails

``` ruby

Agent.all.joins(:mission).where(missions: { objective: "Saving the world" })

```

Just a quick aside about the syntax here, because we are not querying the `Agent` table via `where` here, but the joined `:mission` table, we need to specify that we are looking for specific `missions` in our `WHERE` clause.

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."objective" = ?  [["objective", "Saving the world"]]

```

## Merge

When we want to combine a query on agents and their associated missions that have a specific scope that is defined by you.


We can take two ActiveRecord::Relation objects and can merge their conditions. Sure, no biggie but `merge` is useful if you want to use a certain scope while using an association. In other words, what we can do with `merge` is filter by a named scope on the joined model. In one of the previous examples we used class methods to define such named scopes ourselves.

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

``` ruby

Agent.joins(:mission).merge(Mission.dangerous)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

When we encapsulate what a ```dangerous``` mission is within the `Mission` model, we can now also tuck it on a `join` via `merge`. So moving the logic of such conditions to the relevant model where it belongs is one the one side a nice technique to achieve looser coupling—we don’t want our Active Record models to know a lot of details about each other—and on the other hand, it gives you a nice APi in your joins without blowing up in your face. The example below without merge would not work without an error:

###### Rails

``` ruby

Agent.all.merge(Mission.dangerous)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

When we now merge on an ActiveRecord::Relation object for our missions onto our agents then the database doesn’t know which missions we are talking about. We need to make the association we need clear and join the mission data first—or SQL gets confused.

One last cherry on top. We encapsulate this even better by involving the agents as well: 

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

``` ruby

Agent.double_o_engagements

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = ?  [["enemy", "Ernst Stavro Blofeld"]]

```

That’s some sweet cherry in my book. Encapsulation, proper OOP and great readability. Jackpot!

## has_many

So above we have seen ```belongs_to``` in action a lot. Let’s view this from another perspective and bring secret service sections into the mix:

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

Did you notice something? A small detail is different. The foreign keys are flipped. Here we are requesting a list of sections but use foreign keys like this: ```"agents"."section_id" = "sections."id"```. In other words, We are looking for a foreign key from a table we are joining. On the other hand, our joins via a ```belongs_to``` association looked like this:

###### Rails

``` ruby

Agent.joins(:mission)

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id"

```

Here the foreign keys were mirrored: ```"missions"."id" = "agents"."mission_id"```.
and are looking for the foreign key from the table we are starting.

Going back to your ```has_many``` scenario, we now would get a list of sections that are repeated because they have multiple agents in each section of course. So for each agent column that gets joined on, we get a row for this section / section_id—in short, we are duplicating rows basically.

To make this even more dizzy, let’s bring missions into the mix as well.

###### Rails


``` ruby

Section.joins(agents: :mission)

```

###### SQL

``` sql

SELECT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections"."id" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id"

```

Check out the two `INNER JOIN` parts. Still with me? We are kinda “reaching” through agents to their missions from the agent’s section. Yeah, stuff for fun headaches, I know. What we get are missions that indirectly associate with a ceratain section. As a result, we can new columns joined on but the number of rows is still the same that gets returned by this query. What gets sent back to Active Record—resulting in builing new Ruby objects—is also still the list of sections. So when we have multiple missions going on with multiple agents, we would get duplicated rows for our section again. Let’s filter this some more:

###### Rails

``` ruby

Section.joins(agents: :mission).where(missions: { enemy: "Ernst Stavro Blofeld" })

```

###### SQL

``` sql

SELECT "sections".* FROM "sections" INNER JOIN "agents" ON "agents"."section_id" = "sections"."id" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."enemy" = 'Ernst Stavro Blofeld'

```

Now we only get sections returned that are involved in missions where Ernst Stavro Blofeld is the involved enemy. Kosmopolitan as some super villains might think of themselves, they could operate in more than one section—say section A and C, the United Sates and Canada respectively. If we have multiple agents in a given section who are working on the same mission to stop Blofeld or whatever, we would agin have repeated rows returned to us into Active Record. Let’s be a bit more distinct about it:

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




say there are multiple missions going on in section A, the United States for example. Popular breeding ground for super villains and their shady business.

## Final Thoughts

Getting Active Record to write efficient SQL for you is one of the main skills you should take away from this mini-series. For that it is necessary that you not only understand how to play with Active Record but the underlying SQL is of equal importance. Yes, SQL can be boring, tedious to read and not elegant looking, but don’t forget that Rails wraps Active Record around SQL and you should not neglect understanding this vital piece of technology—just because Rails makes it very easy most of the time to not care. Effeciency is crucial for database queries, especially if you build something for larger audiences and traffic. Therefore 

Getting Active Record to write efficient SQL for you is one of the main skills you should take away from this mini-series for beginners. That way you will also get code that is compliant with whatever database it supports—meaning that the queries will be stable across databases. It is necessary that you not only understand how to play with Active Record but the underlying SQL is of equal importance. Yes, SQL can be boring, tedious to read and not elegant looking, but don’t forget that Rails wraps Active Record around SQL and you should not neglect understanding this vital piece of technology—just because Rails makes it very easy to not care most of the time. Effeciency is crucial for database queries, especially if you build something for larger audiences with heavy traffic. Now go out on the internets and find some more material on SQL to get it out of your system—once and for all!

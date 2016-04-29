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


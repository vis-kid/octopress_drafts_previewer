---
layout: post
title: Queries in Rails 02
date: 2016-04-19 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, Active Record, Queries, Ruby, Ruby on Rails]
---

{% img /images/Rails-Queries/holmes-ruby.png 400 %}

## Topics

+ Includes & Eager Loading
+ Joining Tables
+ Eager Loading
+ Scopes
+ Aggregations
+ Dynamic Finders
+ Specific Fields
+ Custom SQL

In this second article we’ll try to dive a little deeper into Active Record queries in Rails. If you are still new to SQL I’ll still add examples that are simple enough that you can tag along and pick up the syntax as we go. That being said, it would definitely help if you run through a quick SQL tutorial before you come back to continue to read. Otherwise, take your time to understand the SQL queries we used and I hope that by the end of this series it won’t feel intimidating anymore. Most of it is really straightforward but the syntax is a bit weird if you just started out with coding—especially in Ruby. Hang in there, it’s no rocket science!

## Includes & Eager Loading

These queries include more than one database table to work with and might be the most important to take away from this article. It boils down to this: instead of doing multiple queries for information that is spread over multiple tables, `includes` tries to minimize these to a minimum. The key concept behind this is called “eager loading” and means that we are loading associated objects when we do a find.

If we would do that by iterating over a collection of objects and then try to access its associated records from another table, we would run into an issue that is called “N + 1 query problem”. For example, for each `agent.handler` in a collection of agents, we would fire separate queries for both agents and their handlers. That is what we need to avoid since this does not scale at all. Instead, we do the following:

###### Rails

``` ruby

agents = Agent.includes(:handlers)

```

If we would iterate now over such a collection of agents—discounting that we didn’t limit the number of records returned for now—we would end up with two queries instead of possibly a gazillion. 

###### SQL

``` sql

SELECT "agents".* FROM "agents"
SELECT "handlers".* FROM "handlers" WHERE "handlers"."id" IN (1, 2)

```

This one agent in the list has two handlers and when we now ask the agent object for its handlers, no additional database queries need to be fired. We can take this a step further of course and eager load multiple associated table records. If we would need to load not only handlers but also the agent’s associated missions for whatever reason, we could use `includes` like this.

###### Rails

``` ruby

agents = Agent.includes(:handlers, :missions)

```

Simple! Just be careful about using singular and plural versions for the includes. They depend on your model associations. A ```has_many``` associations uses plural while a ```belongs_to``` or a ```has_one``` needs the singular version of course. If you need, you can also tuck on a `where` clause for specifying additional conditions but the preferred way of specifying conditions for associated tables that are eager loaded is by using `joins` instead. 

## Joining Tables

Joining tables is another tool that let’s you avoid sending too many unnecessary queries down the pipeline. A common scenario is joining two tables with a single query that returns some sort of combined records. `joins` is just another finder method of Active Record that lets you—in SQL terms—`JOIN` tables. These queries can return records combined from multiple tables—not just two and you get a virtual table that combines records from two ore more tables. This is pretty rad when you compare that to firing all kinds of queries for each table instead. There are a few different approaches what kind of data overlap you can get with this approach. 

The inner join is the default modus operandi for `join`. This matches all the results that match a certain id and its representation as a foreign key from another object / table. In the example below, put simply: give me all missions where the mission’s `id` shows up as ```mission_id``` in an agent’s table. ```"agents"."mission_id" = "missions"."id"```. Inner joins exclude relationships that don’t exist.

###### Rails

``` ruby

Mission.joins(:agents)

```

###### SQL

``` sql

SELECT "missions".* FROM "missions" INNER JOIN "agents" ON "agents"."mission_id" = "mission"."id"

```

So we are matching missions and their accompanying agents—in a single query! Sure, we could get the missions first, iterate over them one by one and ask for its agents. But then we would go back to our dreadful “N + 1 query problem”. No, thank you! What’s also nice about this approach is that we won’t get any nil cases with inner joins, we only get records returned that match their ids to foreign keys in associated tables. If we need to find missions for example that lack any agents, we would need an outer join instead.

You can also join multiple associated tables of course.

###### Rails

``` ruby

Mission.joins(:agents, :expenses, :handlers)

```

And of course, you can add onto these `where` clauses to specify your finders even more. Below we are looking only for missions that are executed by James Bond and only the agent that belongs to the mission 'Moonraker' in the second example.

###### Rails

``` ruby

Mission.joins(:agents).where( agents: { name: 'James Bond' })

```

###### SQL

``` sql

SELECT "missions".* FROM "missions" INNER JOIN "agents" ON "agents"."mission_id" = "missions"."id" WHERE "agents"."name" = ?  [["name", "James"]]

```

###### Rails

``` ruby

Agent.joins(:mission).where( missions: { mission_name: 'Moonraker' })

```

###### SQL

``` sql

SELECT "agents".* FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."mission_name" = ?  [["mission_name", "Moonraker"]]

```

With `joins` you also have to pay attention to singular and plural use of your model associations. Because my `Mission` class ```has_many :agents``` we can use the plural. On the other hand, the `Agent` class ```belongs_to :mission```, only the singular version works without blowing up. Simple, but important to not overlook. The `where` part is simpler. Since you are scanning for multiple rows in the table that fulfill a certain condition, the plural form makes always sense.

The outer join is the more inclusive join version since it will match all records from the joined tables, even if some of these relationships don’t exist yet. Say agents without a mission or missions without quartermasters and such. Because this approach is not as exclusive as inner joins, you will get a bunch of nils here and there. This can be informative in some cases of course but inner joins is usually what we are looking for. For this to work, we’ll need to write a bit more SQL ourselves—for now. Rails 5 will let us use a specialized method called `left_outer_joins` instead for such cases. Finally!

###### Rails

``` ruby

Agent.joins("LEFT OUTER JOIN missions ON missions.id = agents.mission_id").where(missions: { id: nil })  
```


## Scopes

Scopes are a handy way to extract common query needs into well named methods of your own. That way they are a bit easier to pass around and also possibly easier to understand—if others have to work with your code or if you need to revisit certain queries in the future. You can define them for single models but use them for their associations as well. The sky is the limit really—`joins`, `includes`, `where` are all fair game! Since scopes also return `ActiveRecord::Relations` objects you can chain them and call other scopes on top of them without hesitation. Extracting scopes like that and chaining them for more complex queries is something very handy and makes longer ones all the more readable.

Scopes are defined via the “stabby lambda” syntax:

###### Rails

``` ruby

class Mission < ActiveRecord::Base
  has_many: agents

  scope :successful, -> { where(mission_complete: true) }
end

Mission.successful

```

``` ruby

class Agent < ActiveRecord::Base
  belongs_to :mission

  scope :licenced_to_kill, -> { where(licence_to_kill: true) }
  scope :womanizer,        -> { where(womanizer: true) }
  scope :gambler,          -> { where(gambler: true) } 

end

Agent.licenced_to_kill.womanizer.gambler

```

###### SQL:

``` sql

SELECT "agents".* FROM "agents" WHERE "agents"."licence_to_kill" = ? AND "agents"."womanizer" = ? AND "agents"."gambler" = ?  [["licence_to_kill", "t"], ["womanizer", "t"], ["gambler", "t"]]

```

As you can see from the example above, finding James Bond is much nicer when you can just chain scopes together. That way you can mix and match various queries and stay DRY at the same time. If you need scopes via associations they are at your disposal as well:

``` ruby

Mission.last.agents.licenced_to_kill.womanizer.gambler

```

###### SQL

``` sql

SELECT  "missions".* FROM "missions"  ORDER BY "missions"."id" DESC LIMIT 1
SELECT "agents".* FROM "agents" WHERE "agents"."mission_id" = ? AND "agents"."licence_to_kill" = ? AND "agents"."womanizer" = ? AND "agents"."gambler" = ?  [["mission_id", 33], ["licence_to_kill", "t"], ["womanizer", "t"], ["gambler", "t"]]

```

You can also redefine the ```default_scope``` for when you are looking at something like `Mission.all`.

``` ruby

class Mission < ActiveRecord::Base

  default_scope { where status: "In progress" }

end

Mission.all

```

###### SQL

``` sql

 SELECT "missions".* FROM "missions" WHERE "missions"."status" = ?  [["status", "In progress"]]

```

## Aggregations

This section is not so much advanced in terms of the involved understanding but you will need them more often than not in scenarios that can be considered a bit more advanced than your average finder—like `.all`, `first`, `find_by_id` or whatever. Filtering based on basic calculations for example is most likely something that newbies don’t get in touch with right away. What are we looking at exactly here?:

+ `sum`
+ `minimum`
+ `maximum`
+ `average`
+ `count`

Easy peasy, right? The cool thing is that instead of looping through a returned collection of objects to do these calculations, we can let Active Record do all this work for us and return these results with the queries—in one query preferably. Nice, huh?

+ `count`

##### Rails

``` ruby

Mission.count

# => 24

```

###### SQL

``` sql

SELECT COUNT(*) FROM "missions"

```

+ `average`

###### Rails

``` ruby

Agent.average(:number_of_gadgets).to_f

# => 3.5

```

###### SQL

``` sql

SELECT AVG("agents"."number_of_gadgets") FROM "agents"

```

Since we now know how we can make use of `joins`, we can take this one step further and only ask for the average of gadgets the agents have on a particular mission for example.

###### Rails

``` ruby

Agent.joins(:mission).where(missions: {name: 'Average Mission'}).average(:number_of_gadgets).to_f

# => 3.4

```

###### SQL

``` sql

SELECT AVG("agents"."number_of_gadgets") FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" WHERE "missions"."name" = ?  [["name", "Average Mission"]]

```

Grouping these average number of gadgets by mission’s names becomes trivial at that point.

###### Rails

``` ruby

Agent.joins(:mission).group('missions.name').average(:number_of_gadgets)

```

###### SQL

``` sql

SELECT AVG("agents"."number_of_gadgets") AS average_number_of_gadgets, missions.name AS missions_name FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" GROUP BY missions.name

```

+ `sum`

###### Rails

``` ruby

Agent.sum(:number_of_gadgets) 

Agent.where(licence_to_kill: true).sum(:number_of_gadgets) 

Agent.where.not(licence_to_kill: true).sum(:number_of_gadgets)

```

###### SQL

``` sql

SELECT SUM("agents"."number_of_gadgets") FROM "agents"

SELECT SUM("agents"."number_of_gadgets") FROM "agents" WHERE "agents"."licence_to_kill" = ?  [["licence_to_kill", "t"]]

SELECT SUM("agents"."number_of_gadgets") FROM "agents" WHERE ("agents"."licence_to_kill" != ?)  [["licence_to_kill", "t"]]

```

+ `maximum`

###### Rails

``` ruby

Agent.maximum(:number_of_gadgets)

Agent.where(licence_to_kill: true).maximum(:number_of_gadgets) 

```

###### SQL

``` sql

SELECT MAX("agents"."number_of_gadgets") FROM "agents"

SELECT MAX("agents"."number_of_gadgets") FROM "agents" WHERE "agents"."licence_to_kill" = ?  [["licence_to_kill", "t"]]

```

+ `minimum`

###### Rails

``` ruby

Agent.minimum(:iq)

Agent.where(licence_to_kill: true).minimum(:iq) 

```

###### SQL

``` sql

SELECT MIN("agents"."iq") FROM "agents"

SELECT MIN("agents"."iq") FROM "agents" WHERE "agents"."licence_to_kill" = ?  [["licence_to_kill", "t"]]

```

### Attention!

All of these aggregation methods are not letting you chain on other stuff—they are terminal. The order is important to do calculations. We don’t get an ActiveRecord::Relation object back from these operations which makes the music stop at that point—we get a hash or numbers instead. The examples below won’t work:

###### Rails

``` ruby

Agent.maximum(:number_of_gadgets).where(licence_to_kill: true)

Agent.sum(:number_of_gadgets).where.not(licence_to_kill: true)

Agent.joins(:mission).average(:number_of_gadgets).group('missions.name')

```

### Grouped

If you want the calculations broken down and sorted into logical groups, you should make use of a GROUP clause and not do this in Ruby. What I mean by that is you should avoid iterating over a group which produces potentially tons of queries.

###### Rails

``` ruby

Agent.joins(:mission).group('missions.name').average(:number_of_gadgets)

# => { "Moonraker"=> 4.4, "Octopussy"=> 4.9 }

```

###### SQL

``` sql

SELECT AVG("agents"."number_of_gadgets") AS average_number_of_gadgets, missions.name AS missions_name FROM "agents" INNER JOIN "missions" ON "missions"."id" = "agents"."mission_id" GROUP BY missions.name

```

This example finds all the agents that are grouped to a particular mission and returns a hash with the calculated average number of gadgets as its values—in a single query! Yup! Same goes for the other calculations as well of course. In this case it really makes more sense to let SQL do the work and not use the convenient Rails API. The number of queries we fire for these aggregations is just too important.

## Dynamic Finders

For every attribute on your models, say `name`, `email_address` `favorite_gadget` and so on, Active Record lets you use very readable finder methods that are dynamically created for you. Sounds cryptic, I know but it doesn’t mean anything other than `find_by_id` or `find_by_favorite_gadget`. The `find_by` part is standard and Active Record just tucks on the name of the attribute for you. You can even get to add an `!` if you want that finder to raise an error if not found. The sick part is, you can even chain these dynamic finder methods together. Just like this:

###### Rails

``` ruby

Agent.find_by_name('James Bond')

Agent.find_by_name_and_licence_to_kill('James Bond', true)

```

###### SQL

``` sql

SELECT  "agents".* FROM "agents" WHERE "agents"."name" = ? LIMIT 1  [["name", "James Bond"]]

SELECT  "agents".* FROM "agents" WHERE "agents"."name" = ? AND "agents"."licence_to_kill" = ? LIMIT 1  [["name", "James Bond"], ["licence_to_kill", "t"]]

```

Of course you can go nuts with this but I feel like it looses its charm and usefullness if you go beyond two attributes:

###### Rails

``` ruby

Agent.find_by_name_and_licence_to_kill_and_womanizer_and_gambler_and_number_of_gadgets('James Bond', true, true, true, 3)

```

###### SQL

``` sql

SELECT  "agents".* FROM "agents" WHERE "agents"."name" = ? AND "agents"."licence_to_kill" = ? AND "agents"."womanizer" = ? AND "agents"."gambler" = ? AND "agents"."number_of_gadgets" = ? LIMIT 1  [["name", "James Bond"], ["licence_to_kill", "t"], ["womanizer", "t"], ["gambler", "t"], ["number_of_gadgets", 3]]

```

In this example it is nevertheless nice to see how it works under the hood. Every new `_and_` adds an SQL `AND` operator to logically tie the attributes together. Overall, the main benefit of dynamic finders is its readability—tucking on too many dynamic attributes looses that advantage quickly though. I rarely use this, maybe mostly when I play around in the console, but it’s definitely good to know that Rails offers this neat little trickery.

## Specific Fields

Active Record gives you the option to return objects that are a bit more focused around the attributes they carry. Usually, if not specified otherwise, the query will ask for all the fields in a row via ```*``` (```SELECT  "agents".*```) and then Active Record builds Ruby objects with the complete set of attributes. However, you can `select` only specific fields that should be returned by the query and limit the number of attributes your Ruby objects needs to “carry around”.

###### Rails

``` ruby

Agent.select("name") 

=> #<ActiveRecord::Relation [#<Agent 7: nil, name: "James Bond">, #<Agent id: 8, name: "Q">, ...]>

``` 

###### SQL

``` sql

SELECT "agents"."name" FROM "agents"

```

###### Rails

``` ruby

Agent.select("number, favorite_gadget") 

=> #<ActiveRecord::Relation [#<Agent id: 7, number: '007', favorite_gadget: 'Walther PPK'>, #<Agent id: 8, name: "Q", favorite_gadget: 'Broom Radio'>, ... ]>

```

###### SQL

``` sql

SELECT "agents"."number", "agents"."favorite_gadget" FROM "agents"

```
As you can see, the objects returned will just have the selected attributes plus their ids of course—that is a given with any object. It makes no difference if you use strings like above or symbols—the query will be the same. A word of caution, if you try to access fields on the object that you haven’t selected in your queries, you will receive a `MissingAttributeError`. Since the `id` will be automatically provided for you anyway, you can ask for the id without selecting it though.

###### Rails

``` ruby

Agent.select(:number_of_kills)

Agent.select(:name, :licence_to_kill)

```

## Custom SQL

Last but not least, you can write your own custom SQL via ```find_by_sql```. If you are confident enough in your own SQL-Fu and need some custom calls to the database, this method might come in very handy at times. But this is another story. Just don’t forget to check for Active Record wrapper methods first and avoid reinventing the wheell where Rails tries to meet you more than half way.

###### Rails

``` ruby

Agent.find_by_sql("SELECT * FROM agents")

Agent.find_by_sql("SELECT name, licence_to_kill FROM agents") 

```

Unsurprisingly, this results in:

###### SQL

``` sql

SELECT * FROM agents

SELECT name, licence_to_kill FROM agents

```

Since scopes and your own class methods can be used interchangeably for your custom finder needs, we can take this one step further for more complex SQL queries. 

###### Rails

``` ruby

class Agent < ActiveRecord::Base

  ...

  def self.find_agent_names
    query = <<-SQL
      SELECT name
      FROM agents
    SQL

    self.find_by_sql(query)
  end
end

```
We can write class methods that encapsulate the SQL inside a Here document. That lets us write multi-line strings in a very readable fashion and then store that SQL string inside a variable which we can reuse and pass into ```find_by_sql```. That way we don’t plaster tons of query code inside the method call. If you have more than one place to use this query, it’s DRY as well.

Since this is supposed to be newbie-friendly and not a SQL tutorial per se, I kept the example very minimalistic for a reason. The technique for way more complex queries is quite the same though. It’s easy to imagine having a custom SQL query in there that stretches beyond ten lines of code. Go as nuts as you need to—reasonably! It can be a life saver. A word about the syntax here. The `SQL` part is just an identifier here to mark the beginning and end of the string.

I bet you won’t need this method all that much—let’s hope! It definitely has its place and Rails land wouldn’t be the same without it—in the rare cases that you will absolutely wanna fine tune your own SQL with it.

## Final Thoughts

I hope you got a bit more comfortable writing queries and reading the ol’ dreaded raw SQL. Most of the topics we covered in this article are essential to write queries that deal with more complex business logic. Take your time to understand these and play a bit around with queries in the console. I’m pretty sure that when you leave tutorial land behind, sooner or later your Rails cred will rise significantly if you work on your first real life projects and need to craft your own custom queries. If you are still a bit shy of the topic I’d say, simply have fun with it—it really is no rocket science!

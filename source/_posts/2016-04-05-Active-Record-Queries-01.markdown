---
layout: post
title: Queries in Rails 01
date: 2016-04-05 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, Active Record, Queries, Ruby, Ruby on Rails]
---

{% img /images/Rails-Queries/holmes-ruby.png 400 %}

## Topics

+ Single Objects
+ Multiple Objects
+ Conditions
+ Ordering
+ Limits
+ Group & Having

Active Record is used for querying the database. It can be used with SQL, PostgresSQL and SQLite. For retrieving records from your database you have several finder methods at your disposal. The cool thing about them is that you can save yourself the trouble of writing raw SQL. What does a finder method do really? Basically three things: your provided options get converted into a SQL query. Then the SQL query gets executed and retrieves data from the database. Also, for every row in that results list, we get newly instantiated Ruby objects of the model that corresponds with the query. 

If you haven’t played with SQL before, I’ll try my best to keep things simple and introduce you to the very basics. Follow along the SQL examples and try make sense of these simple queries. SQL is no rocket science really—the syntax just needs a bit time getting used to. This will hopefully wet your appetite to hunt down some useful tutorials that fill in the blanks. 

Let’s have a look at a few methods that are at your disposal:

+ `find` 
+ `first`
+ `last`
+ ```find_by```
+ `all`
+  ```find_each```
+ ```find_in_batches``` 
+ `where`
+ `order`
+ `limit`
+ `offset`
+ `group`
+ `having`

All of these will return an instance of `ActiveRecod::Relation`. A what? It’s a class that is namespaced within the `module ActiveRecord` and it allows us to call multiple query methods and chain them. This object is the heart of the query syntax used in Rails. Let’s check the class of such an object and see for ourselves:

###### Rails

``` ruby

Agent.where(name: 'James Bond').class

# => ActiveRecrod::Relation

```
## Single Objects

+ `find`

This method lets you supply the primary id of an object and retrieves that single object for you. If you provide an array of ids, you can also retrieve multiple objects.

###### Rails

``` ruby

bond = Agent.find(7)

```

###### SQL

``` sql

SELECT  "agents".* FROM "agents" WHERE "agents"."id" = ? LIMIT 1  [["id", 7]]

```

This line of SQL states that you want to select all (```*```) attributes from the `agents` table and “filter” out only the record with the id 7. A limit makes it return only a single record from the database.


+ `first`, `last`

Unsurprisingly, these will give you the first and last records that can be identified by their primary key. The interesting part though is that you can provide an optional number which returns you the first or last of that number of records. 

###### Rails

``` ruby

enemy_agents = SpectreAgent.first(10)

enemy_agents = SpectreAgent.last(10)

```

Under the hood, you are providing a new limit for the number you provide and order it ascending or descending.

###### SQL

``` sql

SELECT  "spectreagents".* FROM "spectreagents"  ORDER BY "spectreagents"."id" ASC LIMIT 10

SELECT  "spectreagents".* FROM "spectreagents"  ORDER BY "spectreagents"."id" DESC LIMIT 10

```

+ ```find_by```

This finder returns the first object that matches the condition you provide.

###### Rails

``` ruby

bond = Agent.find_by(last_name: 'Bond')

```

###### SQL

``` sql

SELECT  "agents".* FROM "agents" WHERE "agents"."last_name" = ? LIMIT 1  [["last_name", "Bond"]]

```

## Multiple Objects

Obviously we often need collection of objects and iterate over them with some agenda. Retrieving a single object or a selected few by hand is nice but more often than not, we want Active Record to retrieve objects in batches. Showing users all kinds of lists is the bread and butter for most Rails apps. What we need is a powerful tool with a convenient API to collect these objects for us—hopefully in a manner that let’s us avoid writing the involved SQL ourselves most of the time.

+ `all`

###### Rails

``` ruby

mi6_agents = Agents.all

```

###### SQL

``` sql

SELECT "agents".* FROM "agents"

```

This method is handy for relatively small collections of objects. Try to imagine doing this on a collection of all Twitter users. No, not a good idea. What we want instead is a more fine-tuned approach for larger table sizes. Fetching the entire table is not gonna scale! Why? Because we would not only ask for a bunch of objects, we would also need to build one object per row in this table and put them into an array in memory. I hope this does not sound like a good idea! So what’s the solution for this? Batches! We are dividing these collections into batches that are easier on memory for processing. Woohoo!

Let’s have a look at ```find_each``` and ```find_in_batches```. Both are similar but behave differently in how they yield objects into blocks. They accept an option to regulate the batch size. The default is 1000.

+ ```find_each```

###### Rails

``` ruby

NewRecruit.find_each do |recruit|
  recruit.start_hellweek
end

```

###### SQL

``` sql

SELECT  "newrecruits".* FROM "newrecruits"  ORDER BY "newrecruits"."id" ASC LIMIT 1000

```

In this case, we retrieve a default batch of 1000 new recruits, yield them to the block and send them to hell week—one by one. Because batches are slicing up collections, we can also tell them where to start via `:start`. Let’s say we wanna process 3000 possible recruits in one go and want to start at 4000.

###### Rails

``` ruby

NewRecruit.find_each(start: 4000, batch_size: 3000) do |recruit|
  recruit.start_hellweek
end

```
###### SQL

``` sql

SELECT  "newrecruits".* FROM "newrecruits" WHERE ("newrecruits"."id" >= 4000)  ORDER BY "newrecruits"."id" ASC LIMIT 3000

```

To reiterate, we first retrieve a batch of 3000 Ruby objects and then send them into the block. `start` let’s us specify the id of records where we want to start fetching this batch.

+ ```find_in_batches```

This one yields its batch as an array to the block—it passes it on to another object that prefers to deal with collections. The SQL is the same here.

###### Rails

``` ruby

NewRecruit.find_in_batches(start: 2700, batch_size: 1350) do |recruits|
  field_kitchen.prepare_food(recruits)
end

```

## Conditions

+ `where`

We need to go over `where` before we continue further. This lets us specify conditions that limit the number of records returned by our queries—a filter for “where” to retrieve records from in the database. If you have played with SQL `WHERE` clauses then you might just feel right at home—same thing with this Ruby wrapper. In SQL, this allows us to specify which table row we want to affect, basically where it meets some sort of criteria. This is an optional clause btw. In the raw SQL below, we select only recruits that are orphans via `WHERE`. 

Select specific row from a table.

###### SQL

``` sql

SELECT * FROM Recruits
WHERE FamilyStatus = 'Orphan';

```

Via `where` you can specify conditions with strings, hashes or arrays. Putting all of this together, Active Record let’s you filter for such conditions like this:

###### Rails

``` ruby

promising_candidates = Recruit.where("family_status = 'orphan'")

```

###### SQL

``` sql

SELECT "recruits".* FROM "recruits" WHERE (family_status = 'orphan')

```

Pretty neat, right? I wanna mention that this is still a find operation—we just specify how we wanna filter this list right away. From the list of all recruits, this will return a filtered list of orphaned candidates. This example is a string condition. Stay away from pure string conditions since they are not considered safe due to their vulnerability to SQL injections.

### Argument Safety

In the example above, we put the `orphan` variable into the string with the conditions. This is considered as a bad practice because it’s unsafe. We need to escape the variable to avoid this security vulnerability. You should read up about SQL injection if this is total news to you—your database might depend on it.

###### Rails

``` ruby

promising_candidates = Recruit.where("family_status = ?", 'orphan'")

```

The `?` will be replaced as the condition value by the next value in the argument list. So the question mark is a placeholder basically. You can also specify multiple conditions with multiple `?` and chain them together. In a real life scenario, we would use a params hash like this for example:

``` ruby

promising_candidates = Recruit.where("family_status = ?", params[:recruits])

```

If you have a large number of variable conditions you should use key/value placeholder conditions.

###### Rails

``` ruby

promising_candidates = Recruit.where(
                        "family_status = :preferred_status 
                         AND iq >= :required_iq
                         AND charming = :lady_killer",
                         { preferred_status: 'orphan', 
                           required_iq: 140,
                           lady_killer: true
                           }
                         )

```

###### SQL

``` sql

SELECT "recruits".* FROM "recruits" WHERE (family_status = 'orphan' AND iq >= 140 AND lady_killer = true)

```

The example above is silly of course but it clearly shows the benefits of the placeholder notation. The hash notation in general is definitely the more readable one.

###### Rails

``` ruby

promising_candidates = Recruit.where(family_status: 'orphan')

promising_candidates = Recruit.where('charming': true)

```

As you can see, you can go with symbols or strings—up to you. Let’s close this section with ranges and negative conditions via NOT.

###### Rails

``` ruby

promising_candidates = Recruit.where(birtday: ('1994-01-01'..'2000-01-01'))

```

Two dots and you can establish any range you need.

``` ruby

promising_candidates = Recruit.where.not(character: 'coward')

```

You can tuck on the `not` onto the `where` to filter out all cowards and get only results that don’t have that specific, unwanted attribute. Under the hood, a `!=` negates the WHERE “filter”.

###### SQL

``` sql

SELECT "recruits".* FROM "recruits" WHERE ("recruits"."character" != ?)  [["character", "coward"]]

```

## Ordering

To not bore you to death with it, let’s make this a quick one.

``` ruby

candidates = Recruit.order(:date_of_birth)

```

``` ruby

candidates = Recruit.order(:date_of_birth, :desc)

```

Apply `:asc` or `:desc` to sort it accordingly. That’s basically it, let’s move on!

## Limits

You can reduce the number of returned records to a specific number. As mentioned before, most of the time you won’t need all records returned. The example below will give you the first five recruits in the database—the first five ids.

###### Rails

``` ruby

five_candidates = Recruit.limit(5)

```

###### SQL

``` sql

SELECT  "recruits".* FROM "recruits" LIMIT 5

```

If you have ever wondered how pagination works under the hood, `limit` and `offset`—in conjunction—do the hard work. `limit` can stand on its own, but `offset` depends on the former.
Setting an offset is mostly useful for pagination and let’s you skip your desired number of rows in the database. Page two of a list of candidates could be looked up like this:

###### Rails

``` ruby

Recruit.limit(20).offset(20)

```

The SQL would look like this:

``` sql

SELECT  "recruits".* FROM "recruits" LIMIT 20 OFFSET 20

```

Again, we are selecting all columns from the `Recruit` database model, limit the records returned to 20 Ruby objects of Class Recruit and jump over the first twenty.

## Group & Having

Let’s say we want a list of recruits that are grouped by their IQs. In SQL this could look something like this.

``` sql

SELECT "recruits".* FROM "recruits" GROUP BY "recruits"."iq"

```

This would get you a list where you’ll see which possible recruits have an IQ of let’s say 120 then another group of say 140 and so on—whatever their IQs are and how many would fall under a specific number. So when two recruits would have the same IQ of 130, they would be grouped together. Another list could be grouped by possible candidates that suffer from claustrophobia, fear of heights or who are medically not fit for diving. The Active Record query would simply look like this:

###### Rails

``` ruby

Candidate.group(:iq)

```

When we count the number of candidates we get back a very useful hash.

###### Rails

``` ruby

Candidate.group(:iq).count

# => { 130=>7, 134=>4, 135=>3, 138=>2, 140=>1, 141=>1 }

```

There we go, we got seven possible recruits with an IQ of 130 and only one with 141. The resulting SQL would look like this:

###### SQL

``` sql

SELECT COUNT(*) AS count_all, iq AS iq FROM "candidates" GROUP BY "candidates"."iq"

```

The important piece is the `GROUP BY` part. As you can see, we use the candidates table to get their ids. What you can also observe from this simple example is how much more convenient the Active Record versions read and write. Imagine doing this by hand on more extravagant examples. Sure, sometimes you have to, but all the time is clearly a pain we can gladly avoid.


We can specify this group even more by using `HAVING`—sort of a filter for the `group`. In that sense, `having` is a sort of `WHERE` clause for `GROUP`. In other words, `having` is dependent on using `group`.

###### Rails

``` ruby

Recruit.having('iq > ?', 134).group(:iq)

```

###### SQL

``` sql 

SELECT "recruits".* FROM "recruits" GROUP BY "recruits"."iq" HAVING iq > '134'

```

We have now grouped or candidates into lists of people that have a minimum IQ of 135. Let’s count them to get some stats:

###### Rails

``` ruby

Recruit.having('iq > ?', 134).group(:iq).count

# => { 135=>3, 138=>2, 140=>1, 141=>1 }

```

###### SQL

``` sql

SELECT COUNT(*) AS count_all, iq AS iq FROM "recruits" GROUP BY "recruits"."iq" HAVING iq > '134'

```

We could also mix and match these and see for example which candidates who have IQs higher than 140 are tied up in relationships or not. 

###### Rails

``` ruby

Recruit.having('iq > ?', 140).group(:family_status)

```

###### SQL

``` sql 

SELECT "recruits".* FROM "recruits" GROUP BY "recruits"."family_status" HAVING iq > '140'

```

Counting these groups is now all too easy:

###### Rails

``` ruby

Recruit.having('iq > ?', 140).group(:family_status).count

# => { "married"=>2, "single"=>1 }

```

###### SQL

``` sql

SELECT COUNT(*) AS count_all, family_status AS family_status FROM "recruits" GROUP BY "recruits"."family_status" HAVING iq > '140'

```

## Final Thougts

I hope this was a useful first look at what Active Record has to offer to make your querying efforts as readable and convenient as possible. Overall I’d say it is an excellent wrapper that keeps you from writing SQL by hand for most of the time. In the next article we’ll look into a couple more involved finders and expand on what we learned so far.

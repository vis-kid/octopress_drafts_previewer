---
layout: post
title: AntiPatterns Basics—Rails Views
date: 2016-01-03 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Views/funky-bridge.gif %}

### Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers. If this is rather new to you and you identify yourself as being more on the beginner side of all things Ruby / Rails, then this one is exactly written for you. I think it’s best if you think of it as a quick skinny-dip into a much deeper topic whose mastery will not happen overnight.Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

### Topics

+ Rails Views
+ PHPitis
+ Markup Mayhem

+ ### Rails Views

Rails comes with ERb out of the box and I think it’s not necessary to throw in cool view-rendering engines like Slim for our examples for now. When you think “convention over configuration” does mostly apply to the model and controller layer you are missing out on a lot of the goodies that makes working with Rails so speedy and progressive. The whole topic includes not only the way you compose your markup but also CSS / Sass, JavaScript, view helpers and your layout templates. Viewed from that angle it becomes a bit deeper than you might think at first. Alone that number of technologies that can be involved in creating your view layer suggest that care should be taken to keep things neat and clear. 

Since the way we write markup and styles is a lot less constrained than domain modeling you want to be extra cautious to keep things simple and flexible. Maintenance should be pretty much your number one priority. Since redesigns or design iterations can be more frequent than extensive changes to your model layer, preparing for change gets a whole nother meaning when it comes to views. My advice, don’t necessarily build for the future but also, by all means, do not underestimate that—especially if you have one of those “idea guys” who knows jack about implementations on the team. What I like about Rails’s approach towards views is that it is treated as equally important and the lessons learned from the model domain were incorporated into the view—whenever possible and useful. Other frameworks seem to agree since they integrated a lot of these ideas pioneered by Rails.

Since the last article was a bit more extensive I chose this topic as a small breather. The following articles about Rails controllers and testing are again bigger in size. The AntiPatterns for views are not that many but they are nevertheless equally important—at least. Since the view is your layer of presentation, maybe you should be especially careful to not create a hazardous mess. Let’s get to it!

+ ### PHPitits

Why do we have MVC in the first place? Yes, because the separation of concerns seemed like the most reasonable thing to do. Sure the implementations of this idea vary a bit here and there but the overall concept of having distinct responsibilities for each layer is the core motivation for building robust applications. Having tons of code in your view layer might not be alien to developers coming from the PHP side of things—although I hear their frameworks have caught up already (heavily influenced by things like Rails?)—but in Ruby land these things have been a loudly voiced AntiPattern—since forever I feel like.

The obvious problems like mixing responsibilities and duplications aside, it simply feels nasty and lazy—a little stupid too to be frank. Sure, I get it, when you develop much within a framework, language or whatever ecosystem it’s easy to become complicit or numb towards crap like that. What I like about the people pushing Ruby is that these things seem to have a lot less weight—might be a reason why innovating never seemed to be a problem within the community. Whatever works best wins the argument and we can move forward.

So is this a whole section dedicated to bash PHP? Not at all! In the past, PHP apps had the reputation of having weak separations between models, views and controllers (Maybe this was one of the core reasons why people felt writing apps with Rails was much more appealing). Having single files stuffed with code for all three layers didn’t seem that sexy. So when we stuff tons of Ruby / domain code into our views it starts to look like the dreaded PHP style of structuring things—PHPitis. Only a few things are as bad as this when it comes to developing web apps I feel. When you care about happy developers and your own future happiness I can’t see why anyone would go down that road. Only pain ahead it seems.

Rails offers a lot of goodies to minimize code in the view as much as possible. You must learn the ways of helpers, layouts and preprocessors in order to achieve a cleaner view. Simple rule of thumb to follow is to keep domain logic out of your views—no shortcuts! The price to pay to be lazy on this is hard to overestimate. The Ruby code that must be in the presentation layer should be as little and as simple as possible as well as intelligently organized. Your reward will be code that is a joy to extend and to maintain—new team members will also have an easier time wrapping their heads around the new codebase. As a bonus, neat freak designers who code also won’t be angy and hide rotten food in your salad if you keep tons of Ruby code out of their markup.



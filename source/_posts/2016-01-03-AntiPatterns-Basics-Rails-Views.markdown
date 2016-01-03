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

Rails comes with ERb out of the box and I think it’s not necessary to throw in cool view-rendering engines like Slim for our examples for now. When you think “convention over configuration” does mostly apply to the model and controller layer you are missing out on a lot of the goodies that makes working with Rails so speedy and progressive. The whole topic includes not only the way you compose your markup but also CSS / Sass, JavaScript, view helpers and your layout templates. Viewed from that angle it becomes a bit deeper than you might think at first.

Alone that number of technologies that can be involved in creating your view layer suggest that care should be taken to keep things neat and clear. Since the way we write markup and styles is a lot less constrained than domain modeling you want to be extra cautious to keep things simple and flexible. What I like about Rails’s approach is that the lessons learned from the model domain were incorporated into the view—whenever possible and useful. Other frameworks seem to agree since they integrated a lot of these ideas pioneered by Rails.

Since the last article was a bit more extensive I chose this topic as a small breather. The following articles about Rails controllers and testing are again bigger in size. The AntiPatterns for views are not that many but they are nevertheless equally important—at least. Since the view is your layer of presentation, maybe you should be especially careful to not create a hazardous mess. Let’s get to it!

+ ### PHPitits



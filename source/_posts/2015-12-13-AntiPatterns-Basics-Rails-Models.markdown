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

AntiPatterns—as the name implies—on the other hand are pretty much the opposite. They are discovers of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices. What they might think they’ll gain in time savings in the beginning is gonna haunt them or some sorry successor later in the project’s lifecycle. Do not underestimate their implications, they’re gonna plague you like a curse—no matter what.

### Topics

+ Fat Models
+ Missing Test Suite
+ Voyeuristic Models
+ Law of Demeter
+ Spaghetti SQL

+ ### Fat Models

I’m sure you have heard the “Fat models, skinny controllers” sing-song tons of times when you first started out with Rails. Ok, now forget that! Sure, the business logic needs to be solved in the model layer but you shouldn’t feel inclined to stuff everything in there senselessly just to avoid crossing the lines into controller territory. Here’s a new target you should aim for, “Skinny models, skinny controllers”. You might ask, “well, how should we arrange code to achieve that—after all it’s a zero-sum game?” Good point! The name of the game is composition and Ruby is well equiped to give you lots of options to avoid model obesity.  

In most (Rails) web applications that are database backed, the majority of your attention and work will be centered around the model layer. Nothing wrong with that. It is therefore only natural that your models will have more gravity and attract more complexity. Active Record also gives you plenty of rope to hang yourself while making your lives incredibly easy. It is a tempting approach to design your model layer if you just follow the path of hightest convenience—after all, a future proof architecture takes a lot more consideration than stuffing business logic into Active Record classes.

+ ### Missing Test Suite 

That is probably the most obvious AntiPattern. Touching a mature app that has not test coverage can be one of the most painful experiences you can encounter. If you wanna hate the world and your own profession more than anything spend six months on such a project. Maybe a week will do as well. I’m pretty sure, the word torture will pop into your mind quite frequently. Not implying that waterboarding is a piece of cake in comparison but that you should not seek such projects the same way you are not looking to get waterboared.

Am I overestimating? I really don’t think so! You want to have a very extensive test coverage, not only because it is a great design tool for only writing code you actually need but also you will need to change your code at some point in the future. You will be a lot better equipped to engage with your codebase—and a lot more confident—if you have a test harness that aides and guides refactorings, maintenance and extensions that come down the road. Zero doubts about that. This is also the point where a test suite starts to pay off the second round of dividends because the increased speed with which you can securely make these quality changes can not be achieved by a long shot in apps are made by people who think writing tests is nonsense or cost too much time. 

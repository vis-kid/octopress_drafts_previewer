---
layout: post
title: AntiPatterns Basics—Rails Controllers
date: 2016-01-25 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Controllers/LomaPrieta-Marina.jpeg %}

## Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

This one is exactly written for you if all this sounds rather new to you and you identify yourself as being more on the beginner side of all things Ruby / Rails. I think, it’s best if you approach these articles as quick skinny-dips into a much deeper topic whose mastery will not happen overnight. Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

## Topics

+ Fat Controllers

Well, “fat models, skinny controllers”, right? In case you haven’t read the previous AntiPattern articles I should mention that you aiming more towards models and controllers that stay skinny is a better guideline—no matter what. All that excess fat is not good for your projects. As with models, you want controllers that have single responsibilities. Controllers should be dumb really, managing traffic and not much else. It is also important that you do not stray much from RESTful controller actions. Sure, every once in a while it can make sense to have additional methods in there but you should feel a little uneasy having them around most of the time. Controllers tend to get fat when they amass business logic or when inexperienced developers don’t make use of Rails’ conventions. As a craftsman, you really should put in the time to know the benefits and limitations of the frameworks you use—at least for commercial purposes where somebody pays for your expertise.

Since controllers are in charge of the flow of your application and for gathering information that your views need, they already have a pretty important responsibility. They really do not need added complexity from the realm of your models. Controllers are closely working with your views to display the data provided by the model layer. Their relationship is tighter than with models. Fat controllers are super common in Rails land. With a little bit of love and care we can avoid that easily in the future. The first step is straightforward. Ask yourself if a controller grows in size if the complexity comes from adding business logic. If so, find a way to move it to the model layer where you have the added benefit of having a better home for testing complex code.

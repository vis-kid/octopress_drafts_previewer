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
+ Voyeuristic Models
+ Law of Demeter
+ Spaghetti SQL

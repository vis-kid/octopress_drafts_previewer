---
layout: post
title: Middleman Basics 03-Podcast Site
date: 2015-11-22 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, Bourbon, Neat, Refills, Middleman]
---

{% img /images/middleman/basics_03_build/Middleman_Bourbon_Banner.png %}

### Topics

+ Roadmap
+ Setup

+ ### Roadmap

Let’s start with a little heads up where this is going. Over the next two articles I’m gonna build a small static site for a ficticious podcast called “Matcha Nerdz” where people dive into all things powdered green tea. It will have the follwing pages:

+ A calendar page
+ A page for each tag
+ A detail page for every episode
+ An index page for previous podcasts

We will use Middleman for generating the static site and the Bourbon suite for all things styling. I expect (hope) that you have taken a look at my articles about Bourbon, Neat and Middleman before you came here. If not, see you in a bit—unless you feel you are already prepared enough of course. I will explain what’s going on along the way but I will not cover the basics as I did previously.

For all things styling, I heavily rely on Bourbon. Also I really dig the indented Sass syntax—I never really got why people are keen to write all that extra junk with the **.scss** syntax. The **.sass** syntax is the only (probably) unfamiliar bit I wanna throw at newbies because its really worth it and I’d kick myself writing it “sassy-ly”. Originally I wanted to use Slim instead of HTML and ERB but I decided against that since I don’t want to introduce too many unknowns for newbies. I want to do something similar with Slim though in the future—mainly because I feel it is the best solution to write concise and intelligent markup out there at the moment. So raincheck. Why not Haml? Simple, Slim is more awesome!



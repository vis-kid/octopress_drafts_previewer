---
layout: post
title: RSpec Testing for Beginners
date: 2016-05-18 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Rails, RSpec, TDD, BDD, Testing, Test-Driven-Development Ruby, Ruby on Rails]
---

{% img /images/RSpec/RSpec-Testing-Red-with-Logo.jpg %}

## Topics

+ What’s The Point?
+ RSpec?
+ Syntax
+ Running Tests
+ Organizing Tests

## What’s The Point?

Test-driven development, what’s the point? Well, that is not that easy to answer without feeding you some clichés. I hope this doesn’t sound evasive. I could provide a quick answer but I want to avoid sending you home with an empty stomach after having some snack. The result of this little series about RSpec and testing should not only give you all the info to answer this question yourself but also provide you with the means and the understanding to get started with testing while feeling confident in your shoes.

Beginners seem to have a harder time getting into RSpec and the whole testing workflow than starting to get dangerous with Ruby or Rails. Why is that? I can only guess at this point but on the one hand, the literature seems mostly focused on people who already have some programming skills under their belt and on the other hand, learning all the stuff that is involved to have a clear understanding is a bit daunting. The learning curve can be quite steep I suppose. For effective testing, there are a lot of moving parts involved. It’s a lot to ask for newbies who have just started to understand a framework like Rails to look at the process of building an app from the other perspective and learn a complete new API to write code for your code.

I thought about how to approach this “dilemma” for the next generation of coders who are just looking for a smoother start into this whole thing. This is what I came up with. I will break the most essential syntax down for you without assuming much more than basic knowledge of Ruby and a little bit of Rails. Instead of covering every angle possible and confuse you to death, we will go over your basic survival kit which. We will discuss the “How?” rather verbosely to not loose new coders along the way. The second part of the equation will be explaining the “Why?”. If I’m lucky, you’ll get away with a good basis for more advanced books while feeling confident about the bigger picture? Ok now, let’s walk the walk! 

### Benefits And Such

Let’s get back at the purpose of testing. Is testing useful for writing better quality apps? Well, this can be hotly debated, but at the moment I’d answer this questions with a yes—means I’m in the hipster TDD camp I guess. Let’s see why tests provide your apps with a couple of benefits that are hard to ignore:


+ They check if your work functions as intended.

+ They test stuff you don’t want to test by hand, tedious checks that you could do by hand—especially when you’d need to check this all the time. You want to be as sure as possible that your new function or your new class or whatever does not cause any side effects in maybe completely unforeseen areas of your app. Automating that sort of a thing saves you not only a TON of time but will also make testing scenarios consistent and reproduceable. That alone makes them much more dependable than error-prone testing by hand.

+ Automating tests make you actually test more frequently. Imagine if you have to test something for the 40th time for some reason. If it is only a bit time consuming, how easy will it be to get bored and skip the process altogether. These sort of things are the first step on a slippery slope where you can kiss a decent percentage of code coverage goodbye.

+ Tests work as documentation. Huh? The specs you write gives other people on your teams a quick entry point to learn a new code base and understand what it is supposed to do. Writing your code in RSpec for example is very expressive and forms highly readable blocks of code that tell a story if done right. Because it can be written very descriptive while also being a very concise Domain Specific Language (DSL), RSpec hits two birds with one stone. Not being verbose in it’s API and providing you with all the means to write highly understandable test scenarios. That’s what I always liked about it and why I never got really warm with [Cucumber](https://cucumber.io/) which was solving the same issue overly client friendly I think.

+ They can minimize the amount of code you write. Instead of spiking around like crazy, trying out stuff more freestyle, the practice of test-driving your code let’s you write only the code that is necessary to pass your tests. No excess code. A thing you will often hear in your future career is that the best code is no code. Why? Well, most often, more elegant solutions involve lesser amounts of code and also, code that you don’t write—which might be unnecessary btw—won’t cause any future bugs. So writing the tests first, before you write the implementation, gives you a clear focus which problem you need to solve next. 

+ They have a positive effect on your design. To me, understanding this part turned on a light bulb and made me really appreciate the whole testing thing. When you write your implementations around very focused test scenarios, your code will most likely turn out to be much more compartmentalized and modular. Since we are all friends of DRY—“Don’t repeat yourself!”—and as little coupling between components in your app as possible, this a simple but effective discipline to achieve systems which are designed well from the ground up. This aspect is the most important benefit I think. Yes, the other ones are pretty awesome as well, but when tests also result in apps which quality is better due to a refined design, I’d say Jackpot! 











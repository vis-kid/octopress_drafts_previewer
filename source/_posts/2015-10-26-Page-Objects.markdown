---
layout: post
title: Page Ojbects for Capybara Connoisseurs
date: 2015-10-26 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

{% img /images/Factory-Girl-Guide/Factory_Guide_Association_cropped.png %}


### Topics

+ What are Page Objects
+ When and why?
+ Different type of Page Objects
+ Acceptance test / feature specs
+ Refactoring
+ How?
+ Design Patterns?
+ Tell-Don’t-Ask

+ ### What are Page Objects?

Encapsulation is the key concept with Page Objects.

Its a way to encapsulate behaviour driving through a test flow.

You encapsulate markup and page interactions

It caputures interactions with a particular set of elements on a page.

might describe the full page itself

or multiple pages in sequences as you go through a flow

as opposed to spreading that logic across a lot of your specs -> DRY

Its a mixture of a couple of refactoring techniques: 
Extract class
Extract method

They are even more high-level than Capybara feature specs

There are many refactorings possible for your feature specs but Page Objects offerthe cleanest abstractions for encapsulating user facing behaviour on and page or flows.

Testing against web pages means the need to refer to DOM elements and interact with them. Doint this without Page Objects introduces duplication and brittle tests when the UI changes.

You create an APi that is specific to your application and your feature spec needs. 

That way you can play with Pages and their elements—all without fiddling around with DOM elements. -> You can emulate the human experience quite expressively.

You don’t have to simulate the whole page though—focus on the essential bits that are necessary for user flows. No need to overdo it!

Its a good design principle to floww when complexity of your feature specs increase.

Tests for client code become easier to understand.

It promotes reuse
centralizing UI elements and isolate impact of changes
Expressive DSL

A design pattern to refactor your feature specs

Write acceptance tests with application language

HTML implementation details in one p lace.

Tests are more stable

UI interaction tests have now same quality as test for application code.

Due to changes, Capybara selectors introduce breakpoints. Let’s say a designer wantes to change the text on a button. Do you want to make that change in cone central wrapper for that provides that element to your specs or do you prefer to do that all over the place? I thought so!





+ ### When & Why?

Its a good idea to apply this design pattern a little bit later in a projects life cycle—when you have amassed a little bit of complexity in your feature specs and when you can identify repeating patterns like DOM structures or other commonalities that are consistent on your pages. So you maybe you shouldn’t start writing Page Objects right away. You approach these refactorings gradually when the complexity and size of your application and their tests grow. Duplication that need a better home through Page Objects will be easy to spot over time.

Page Objects provide you the opportunity to write clearer specs that read better and are overall a lot more expressive because they are more high-level. Beside that, they offer a nice abstraction for everybody who like to write OO code.

When design implementation change, your description of how your app should work doesn’t need to change via Page Objects. These feature specs are more focused with the user level and not so much about the specifics of the DOM implementations.

Page Objects hide the specifics of the DOM and also enable you to have private methods that do the dirty work and which are unexposed to the public API. Extracted methods in your feature specs don’t offer the same luxury. The API of Page Objects don’t need to share the nitty-gritty Capybare details

Page Objects become critical when applications grow and aids also the understanding when the size of the application also means drastically increased complexity.



+ ### Different type of Page Objects

+ Components
+ Pages
+ Experiences

*Components* represent the smallest units and are more focused—like a form object for example. *Pages* combine more of these components and are abstractions of a full page. And you guessed it by now, *experiences* span the whole flow across potentially many different pages. They are more high-level. The focus on the flow the user experiences while they interact with various pages. A checkout flow which has a couple of steps is a good example to illustrate this idea.




+ ### Refactoing

You identify commonalities, like a structure in your markup that is consistent within your pages. Something like a <ul><li> for example. This is unlikely to change and easy to reuse in your tests. Forms are another common example. Through these refactorings your Page Objects become the canonical place to centralize the elements and behaviour sets that you need. And you specify the flow in one place too.




+ ### How?

You create expressive methods on your Page Objects which are more high-level and read better. You basically extract methods into Page Objects. You do the fiddling with the intestines of your markup behind these methods. Shotgun surgery is not your problem anymore that way.

You abstract away a lot of the noisy down in the weeds code about interacting the the DOM. 

You create largely a language via the API that a user or a non-technical stakeholder on a team might use. Ask yourself the question: How would a user describe the flow? That should be a good measure for method names on Page Objects. 

No concern about the DOM structure

You can focus on the functionality and less on the actual structure of the markup which is now encapsulated in a Page Object.

RSpec generates matchers based on predicate methods on your Page Objects. has_? becomes RSpec matcher. RSpec converts them by removing the ? and changes has to have. Boom, matchers from scratch without much fuzz! A bit magic, I’ll give you that, but the good kind of wizardry I’d say. 

spec/support/features/pages

Plain old Ruby object.

Page Objects are in essence very simple classes really. 

include Capybara::DSL

for finding elements, clicking links etc.

normally you don’t instantiate Page Objects with data. 

You hide away the Capybara specifics you need.



#### Attention!

Setup like factory data belongs in the specs and not into Page Objects.

Also assertions are probably better be outside of your Page Objects to achieve a separation of concerns.

There are two differnt perspectives on the topic:
Having assertions on Page Objects or just providing data for specs that do the assertions.

Advocates for putting assertions into Page Objects say it helps with duplication of assertions. You can provide better error messages and achieve a better Tell-Don’t-Ask style.

Advocates for assertion-free Page Objects argue that its better to not mix responsibilities. Providing access to page data and assertion logic are two separate concerns and lead to bloated Page Objects when mixed. Page Objects responsibilities is access to the state of pages and assertion logic belongs to specs.









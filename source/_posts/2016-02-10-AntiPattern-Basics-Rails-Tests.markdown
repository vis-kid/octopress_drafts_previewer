---
layout: post
title: AntiPatterns Basics Rails—Tests
date: 2016-02-10 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, TDD, BDD, Test-Driven-Development, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Tests/electric-wiring.jpg 500 %}

## Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

This one is exactly written for you if all this sounds rather new to you and you identify yourself as being more on the beginner side of all things Ruby / Rails. I think, it’s best if you approach these articles as quick skinny-dips into a much deeper topic whose mastery will not happen overnight. Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

## Topics

+ Slow Tests
+ Let
+ Mystery Guest
+ Obscure Tests
+ RSpec DSL Puzzle
+ Brittle Tests
+ Fixtures
+ Factories
+ Uncertainty of Time???

## Slow Tests

So, you have started to get into Test-Driven-Development and you started to appreciate what if offers. Kudos, this is great! I’m sure, neither the decision to do it nor the learning curve to get there were exactly a piece of cake. But what often happens after this step is that you try hard at having full test coverage and you start to realize that something is off. Your test suite is getting slower and slower although you think you are doing all the right things. Why am I feeling punished for this, right? Slow tests can suck—big time! There are a couple of problems with them. The most important issue is that slow tests lead to skipping tests in the long run. Once you are at a point where your test suite takes forever to finish, you will be much more willing to thing to yourself: ‘Screw this! I got better things to do than waiting for this stuff to finish.” And you are absolutely right, you have better things to do than wait.

The thing is that slow test are more likely to welcome in compromises in the quality of your code than maybe obvious. Slow tests also fuel people’s arguments against TDD—unfairly so I think. When you have little time to set up your tests and super quick feeback cycles to develop each step of a new feature, TDD becomes a lot more attractive and less of an argument. Don’t even want to know what non-technical, clueless managers have to say if you have to step outside for a 20 minute coffee break just to run your test suite. Let’s not go down that road. With a little bit of work and care along the way we can avoid slow-mo tests quite effectively. Slow tests are also a killer for getting into the “zone”. You want to get as much zone time as possible. If you get taken out of the flow this frequently in your process, the quality of your overall work might also suffer by having to wait for slow tests to return from an expensive round trip.

unbearably slow !!!

Another issue worth mentioning in this context is that this might lead to having tests that cover your code, but because you won’t take time to finish exercising the whole suite, or write tests after the fact, your apps’ design won’t be driven by tests anymore. If you are not on the Test-Driven train this might not bother you much, but for people who have drunk this kool-aid, that aspect is essential to testing and should not be neglected. Bottom line, the faster your tests, the more you will be willing to exercise them—which is the best way to catch bugs early and often. What can we do to speed them up or to avoid to getting into snail mode? There are two speeds that are important:

+ The speed for getting feedback from your test suite to design your app.
+ The speed in which your tests can really execute your suite.

### Getting feedback from your app / tests fast.


You want that dialog to be fast since talking in slow-mo can be painful. Tightening this feedback cycle by using an editor that can also exercise your tests is not to be underestimated. Switching back and forth between your editor and your terminal is NOT the best solution to handle this. This gets old very quickly. If you like using Vim, you have one more reason to invest some time to become more efficient at using your editor. Sublime Text also has some option for that I remember but oher than that, you need to do a little bit of research of your own if you are into other stuff. The argument that you will hear frequently from TDD enthusiasts is that you don’t want to leave your editor because you are spending too much time overall doing that. You want to stay much more in the zone and not lose train of thought when you can do this sorta thing via a fast shortcut from inside your code editor. 

Another thing to note is that you also want to be able to slice the tests that you want to run. If you don’t need to run the whole file, it’s nice to run a single test or a block that focuses just on what you need to get feedback on right now. Having shortcuts that help you run single tests, single files or just the last test again saves you a ton of time and keeps you in the zone with a high degree of convenience. I gotta say, it makes you feel god damn cool as well. It’s just amazing how awesome coding tools can be sometimes.

Spring???
Zeus???

### Avoid writing to the database as much as you can.

That does not mean that you should avoid it all costs, just that often you don’t need to write tests that exercise the database which can trim off a lot of time that your tests need to run. Using just ```new``` to instantiate an object is often sufficient for tests cases. Faking out objects that are not directly under test is another viable option. Creating test doubles for examples is a a nice way to make your tests faster while keeping the collaborating objects you need for your setup super focused and lightweight. Factory Girl also gives you various options to “create” your test data. But sometimes there is not way around to saving to the database and this is exactly the scenraio where you should do it. Any other time, avoid it like hell and your test suite wil stay fast and agile. In that regard you should also aim for a minimal amount of dependency, which means the minimal amount of objects that you need to get your tests to pass—and save as less as possible to the database along the way. Stubbing out objects—that are mere collaborators for tests—instead of writing to the database not only makes your setup easier to digest and simpler to create, it also gives you a nice speed boost overall.

### Build your tests with the testing pyramid in mind.

This means that you want to have a majority of unit test at the bottom of this hierarchy—which all focus on very specific parts of your application in isolation, and the smallest amount of integration tests at the top of this pyramid—which exercises a user going through your system and interacting with a bunch of components that are exercised around the same time. They are easy to write but not so easy to maintain—and the speed losses are not worth going the easy route. The integration tests are pretty much the opposite of unit tests in regard to them being high level and sucking in a lot of components that you need to set up in your tests—which is one major reason why they are slower than unit tests and too much integration tests can cause significant speed losses. What’s also an important issue here is that you want to have as little overlap between these two test categories as possible—you ideally want to test things only once after all. You can’t expect to have perfect separation between them, but aiming for as little as possible is a reasonable and achievable goal.

In contrast to unit tests, you want to test as few details as possible with integration tests. That should already be covered by extensive, focus unit tests. Focus on the most essential parts that the interactions need to be capable of exercising! The other main reason is that a webdriver needs to simulate going through a browser and interacting with a page. This approach fakes out nothing or very little, saves the stuff to the database and really goes through the UI. That’s also one reason they can be called acceptance tests because these tests try to simulate a real user experience. Another major speed bump that you want to exercise as little as possible. If you have a ton of these, I guess more than 10% from your overall number of tests, you should slow down and reduce that number to the minimum amount possible. Also, another approach to keep in mind is not to exercise the whole app for an integration test when a smaller, focused view test would do the trick as well. You will be much faster if you rewrite a couple of your integration tests that just test a little bit of logic that does not necessitate a full integration check. That being said, intergration tests are vital to the quality of your tests and you need to find a balance of being too stingy applying them and not having too much of them around.

Large, five minute plus test suites can easily lead to bad practices. A slow test suite like this does not aid you much in effectively designing your application. Quick feedback and fast iteration cycles are key to designing your objects. Once you start to keep pushing to run these tests some time later because it take so long to finish, you are loosing this advantage which is a big aid for quiality object design. Don’t wait until your Continious Intergration service of choice comes into play to test your whole application. So what’s a magic number we should aim for in terms of overall time a test suite should be taking to run all tests? Well, different people will tell you different benchmarks for this. I think that staying under 30 secons is a very reasonable number that makes it very likely to exercise a full test on a regular basis. If you leave that benchmark more and more behind, I think you have some work to do to get it back in check. It will be worth it and it will make you feel much more comfortable going forward because you can check in more regularly and can move forward a lot faster that way.

## Let

The ```let``` helper method in RSpec is very frequently (over?)used for creating instance variables that are available between multiple tests. Following this practice can easily lead to having lots of mystery guests showing up and as we’ll address in just a bit , that is not something we need to crash our party—ever! This side effect has gained a bit of a reputation to be possibly causing increased test maintenance and inferior readability throughout your test suite. It sure sounds good because it’s lazily evaluated and aids adhering to the usually zero-defect concept of DRY and all, so it obviously seems too good not to use on a regular basis. What follows below also relates to ```subject```—which we can avoid to use also.

An all-time favorite are let statements that are plastered all over nested ```describe``` blocks. I think it’s not unfair to call this a recipe for hanging your self in motion.

More limited scope is generally easier to understand and follow. We don’t want to build a house of cards with semi-global let fixtures that obscure understanding and increase chances of breaking related tests. The odds of crafting quality code with this approach are stacked against us with this approach.

Extracting common object setup is also easier to do via plain old ruby methods or even classes if need be.

These let fixtures can amass a lot of attributes and methods that make them way too big as well.

We want to have as few collaborators and as little data as possible for each test. let works not in your favor on that as well. If you start going down the let road, you will often end up with pretty big objects that try to make a lot of tests happy at the same time. Sure you can create lots of lets but that makes the whole idea of lets maybe irrelevant. That way you can just go one step further and avoid let and rely on Ruby without RSpec DSL magic.

I’m personally in the camp of being rather on the side of repeated setup code for each test than being overly DRY, obscure or cryptic in the test suite. I’d go always for more readablility in my tests. The test method should make clear the cause and effect of its involved pieces—defining parts of it possibly far away is not in your best interest here. If you need to extract stuff, expressive methods that encapsulate that knowledge are often a better bet. It also helps to create the setup for each test that you actually need and not cause slow tests because you have more data than needed for the individual tests methods. Good old variables, methods and classes are often all you need to provide faster, more stable and easier to read tests.

Let are largely shared fixtures which you will need to decipher first before you know exactly why this object is showing up in your test and what exactly it is made of—or which relationships it has via associations. The clarity of these details when you setup up these objects for a test scenario usually help a lot to tell developers all the info they need to work with this particular part of your test suite. In a world where you never have to revisit particular tests and even never refactor parts of your test suite that might not matter as much—but that is a pipe dream for now!

## Fixtures

I’m not sure if fixtures are still an issue for newbies coming to Ruby / Rails land, but in case nobody instructed you about it or you were paying attention to something far more interesting in the meantime, I’ll try to get you up to speed on these dreaded things in a jiffy. ActiveRecord database fixtures are a great examples of having tons of Mystery Guests in your test suite. In the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up test data in your application. They played an important role and helped move the industry forward. Nowadays they have a reasonably bad rep though. The hash-like structure sure looks like handy and easy to use. You can reference other nodes within each other if you want to simulate associations from your models. But I think its fair to say that that’s where the music stops and many say their pain started. For data sets that are a bit more involved, YAML fixtures are difficult to maintain and hard to change without affecting other tests. I mean you can make them work of course—after all developers used them plenty in the past—but many people agreed that the price to pay for managing fixtures is just a bit stingy.

One scenario we all would definitely like to avoid is changing little details on an existing fixture and causing tons of tests to call for a time-out—if they are unrelated, the situation is even worse—a good example of tests being too brittle btw. In order to “protect” existing tests, this can also lead to growing your fixture set beyond any reasonable size—being DRY with fixtures is at that point most likely not on the table anymore as well. To avoid breaking your test data when the inevitable changes occur, developers where happy to adopt newer strategies that offered more flexibility and dynamic behaviour. That’s where Factory Girl came in and kissed the YAML days goodbye. Another issue is the heavy dependency between the test and the .yml fixture file. Since the fixtures are defined in a separte .yml file, mystery guests are also a major pain waiting to bite you. Did I mention that fixtures are imported into the test database without running through any validations and don’t adhere to the Active Record life cycle? Yeah, that’s not awesome from whatever angle you wanna look at it! 

Factory Girl let’s you avoid that by creating objects relevant to the tests inline—and only with the data needed for that specific case. The motto is, only define the bare minimum in your factory definitions and add the rest in a test-by-test basis. Locally (in your tests) overriding default values defined in your factories is a much better approach than having tons of fixture unicorns waiting to be outdated in a fixture file—this approach is more scalable too. Factory Girl gives you plenty of tools to create all the data you need—as nuanced as you like—but also provides you tons of ammo to stay DRY where needed. The pros and cons are nicely balanced with this one. Not dealing with validations is also not a cause of concern anymore. I think the factory pattern approach is more than pretty reasonable and was one major cause why Factory Girl was so well received by the community. Complexity is a fast growing enemy that YAML fixtures are hardly equipped to take on effectively. In some way I think of fixtures also as ```let``` on steroids. You are not only placing them even further away—being in a separate file and all—you are also potentially preloading way more fixtures than you might actually need. RIP!

## Mystery Guest
It’s an RSpec DSL Puzzle really. For a while the various objects defined via RSpec DSL are not that hard to keep in check but after a while when the test suite grows you invite a lot of mystry guests into your tests and give your future self and others unnecessart context puzzles to solve. The result will be obscure tests that require you to go Sherlock Holmes on your tests and deduct which objects go where and what developer killed what object to cause failing tests that have gone brittle.

Myster guest:
+ There is this object coming from?
+ What exactly is it composed of?

Solution: Fresh “fixture”, where you build a local version of the object with exactly the date that you need—and not more than that! Factory Girl is a good choice for handling this. It can be considered more verbose and you might be duplicating stuff sometimes—extracting this into a method is a good idea in that case—but it is a lot more expressive and keeps tests focused and in context.

``` ruby

describe Mission do
  let(:agent_01)   { build_stubbed(:agent, name: 'James Bond', number: '007') }
  let(:agent_02)   { build_stubbed(:agent, name: 'Moneypenny', number: '24') }
  let(:title)   { 'Moonraker' }
  let(:mission) { build_stubbed(:mission, title: title) }
  mission.agents << agent_01 << agent_02

  #...
  #...
  #...
  #lots of other tests

  describe '#top_agent' do
    it 'returns highest ranking agent associated to a mission' do
      expect(mission.top_agent).to eq('James Bond')
    end
  end
end
 
```

This describe block lacks clarity and context. What agent is involved and what mission are we talking about here. You force developers to go hunting for objects that are suddenly popping up in your tests. Classic example of mystery guests showing up uninvited to your party. When we have lots of code between the relevant test and the origin of these objects you increase the chances of wasting everybody’s time without good cause.

``` ruby

describe Mission do

  #...
  #...
  #...
  #lots of other tests

  describe '#top_agent' do
    it 'returns a list of all agents associated to a mission' do
      agent_01 = build_stubbed(:agent, name: 'James Bond', number '007')
      agent_02 = build_stubbed(:agent, name: 'Moneypenny', number '007')
      mission  = build_stubbed(:mission, title: 'Moonraker')
      mission.agents << agent_01 << agent_02
  
      expect(mission.top_agent).to eq('James Bond')
    end
  end
end
```

### Obscurity

This example builds all the objects in question needed for our tests in the actual test case and provides all the context one needs. The developer can stay focused on a particular test case and does not need to “download” another—possibly totally unrelated—test case for dealing with the situaion at hand. No more obscurity! A good reason to use FactoryGirl over fixtures is that fixture objects defined in yet another file are even more obscure—and I’m not even yet talking about fixtures being inflexible and non-DRY at all here. Mystery guests are not welcome to our RSpec parties.

Yes, you are right, this approach means that we are not achieving the lowest level of duplication possible, but clarity in these cases is mucn more important for the quality of your test suite and therefore for the robustness of your project—the speed in which you can effectively apply changes to your tests plays also a role in that regard. Another important aspect of testing is that your test suite not only can function as documentation but absolutely should! Zero duplication is not a goal that has a positive effect for tests documenting your app. The creation of objects is an important part of providing the context needed to understand particular functionalities fast and efficient. Keeping unnecessary duplication in check is nevertheless an important goal to not loose sight of though—balance is king here!  

## Brittle Tests

If changes lead to seemingly unrelated failures in tests, you are likely looking at a test suite that has become fragile due to causes mentioned above. These often puzzle-like, mystery guest infested tests easily lead to tests that are more brittle than you might expect. When objects necessary for tests are defined “far away” from the actual test scenario, it’s not that hard to overlook the relationships that these objects have with their tests and when code gets deleted, adjusted or simply the setup object in question gets accidentally overriden—totally unaware how this could influence other tests around—failing tests are not a rare sight to be encountered—which easily look like they are totally unrelated failures. I think it’s fair to include such scenarios into the cateogory of tightly coupled code.

``` ruby

describe Mission do
  let(:agent)   { build_stubbed(:agent, name: 'James Bond', number: '007') }
  let(:title)   { 'Moonraker' }
  let(:mission) { build_stubbed(:mission, title: title) }
  mission.agents << agent_01

  #...
  #...
  #...
  #lots of other tests

  describe '#joint_operation_agent_name' do
    let(:agent) { build_stubbed(:agent, name: 'Felix Leiter', agency: 'CIA')
    mission.agents << agent

    it “returns mission’s joint operation’s agent name” do
      expect(mission.joint_operation_agent_name).to eq('Felix Leiter')
    end
  end
end

```

In this scenario we have clearly modified locally the objects’s state defined in our setup. The ```agent``` in question is now a CIA operative and has a different name. ```mission``` comes again out of nowhere as well. Not obvious but nasty stuff really. Let’s get rid of the ```let``` nonsense and build the objects we need again right where we test them—with only the attributes we need for the test case of course. 
 
``` ruby

describe Mission do

  #...
  #...
  #...
  #lots of other tests

  describe '#joint_operation_agent_name' do
    agent   = build_stubbed(:agent, name: 'Felix Leiter', agency: 'CIA')
    mission = build_stubbed(:mission)
    mission.agents << agent

    it “returns mission’s joint operation’s agent name” do
      expect(mission.joint_operation_agent_name).to eq('Felix Leiter')
    end
  end
end

```

``` ruby

context "mission’s agent status" do
  let(:agent) {build_stubbed(:agent, name: 'James Bond', status: 'Missing in action') }

  it 'returns the agent’s status' do
    expect(mission.agent_status).to eq('Missing in action')
  end
end

context "mission’s agent status" do
  it 'returns the agent’s number' do
    007 = build_stubbed(:agent)
    mission = build_stubbed(:mission, agent: 007)

    expect(mission.agent_status).to eq(007.status)
  end
end

```

Here we are overriding the agent that we created via ```let```. In such cases you want to be more clear about how these objects are being used and how they are related. As you can see in the second ```context```, we override the agent defined in ```let``` and  give it another specific name but don’t have the context of the agents ```status``` anymore. It is important to understand how they are related and how they are used—especially if you don’t want to send other developers on a wild goose chase to figure this out before they can continue their work on something new or totally unrelated. If it’s super hard to get a grasp quickly and a new feature needs to be implemented blazingly fast, these puzzles can not expect the highest priority, which means that new stuff get’s developed on top of that unclear context—which in turn is a brittle basis for going forward and also super inviting to bugs down the road.

Also, guess what happens if somebody decides to rename the ```agent``` object defined in ```let``` and introduces another vector for failing tests that way. Loose coupling is your very best friend here as well. 

The relationship between the ```007.status``` and the mission status could be confusing since it’s coming out of nowhere really due to redefining the ```agent``` variable. Also, the second version is overall not very descriptive.

``` ruby

context "mission’s agent status" do
  it 'returns the agent’s number' do
    007 = build_stubbed(:agent)
    mission = build_stubbed(:mission, agent: 007)

    expect(mission.agent_status).to eq(007.status)
  end
end

```

### Data Attributes

A final useful tip for avoiding brittle tests is to use data attributes in your HTML tags. This lets you decouple the needed elements under test from the styling information that your designers might use—and change without your involvement. If you hard code a class like ```class='mission-wrapper'``` and a wise designer decides to change this name because this is probably not the best name you could come up with, your test will be affected unnecessarily. And the designer is of course not to blame because how in the world would he know that this affects part of your test suite—very unlikely at least. 

``` erb

<div class='mission data-role='single-mission'>
  <h2><% = @mission.name %></h2>
  ...
</div>

```

``` ruby

context "mission’s agent status" do
  it 'does something with a mission' do
    ...

    ...

    expect(page). to have_css '[data-role=single-mission]'
  end
end

```

If you expect to see some element on a page and that element can be signified by a ```data-role``` that designers have no reason to touch, you can protect yourself from brittle tests that happen due to changes on the styling side of things. Pretty effective and useful strategy that basically costs you nothing in return. The only thing that might be necessary is to have a short conversation with designers to not touch them. Piece of cake!





huge burden

We want to avoid distracting people who will read our tests or even worse, confuse them. That is openeing the door for bugs but can also be expensive cause it can cost valuable time to be billed. When you create your tests, try hard not to override things. It does not aid in creating clarity, it can lead to subtle, time-consuming bugs and it does not affect the aspect of documenting your code positively. Mutating data more than absolutely necessary—when you test for a mutation for example—is also worth keeping in mind. Again, this helps avoiding sending other developers or your future self on  wild goose chases.

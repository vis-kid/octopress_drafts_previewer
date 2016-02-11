---
layout: post
title: AntiPatterns Basics Rails—Tests
date: 2016-02-10 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [AntiPatterns, TDD, BDD, Test-Driven-Development, Ruby, Rails, Code Smells, Refactoring, Object Oriented Programming ]
---

{% img /images/AntiPatterns/Tests/rotten-pear-cropped.jpg %}

## Heads Up

Anti- what? It probably sounds a lot more complicated than it is. Over the last couple of decades, programmers were able to identify a useful selection of “design” patterns that frequently occurred throughout their code solutions. While solving similar problems, they were able to classify solutions that prevented them from reinventing the wheel for every project. It is important to note that these patterns should be seen more as discoveries than the inventions of a group of advanced developers.

AntiPatterns—as the name implies—on the other hand represent pretty much the opposite. They are discoveries of solutions to problems that you should definitely avoid. They often represent the work of inexperienced coders who don’t know what they don’t know yet. Worse, it could be the output of a lazy person who just ignores best practices for no good reason—or they think they don’t. What they might hope to gain in time savings in the beginning by hammering out quick, lazy or dirty solutions is gonna haunt them or some sorry successor later in the project’s life cycle. Do not underestimate the implications or these bad decisions, they’re gonna plague you like a curse—no matter what.

This one is exactly written for you if all this sounds rather new to you and you identify yourself as being more on the beginner side of all things Ruby / Rails. I think, it’s best if you approach these articles as quick skinny-dips into a much deeper topic whose mastery will not happen overnight. Nevertheless, I strongly believe that starting to get into this early will benefit beginners and their mentors tremendously.

## Topics

+ Mystery Guest
+ Obscure Tests
+ RSpec DSL Puzzle
+ Brittle Tests
+ Let!
+ Slow Tests
+ Fixtures
+ Factories
+ Uncertainty of Time???

## Let

The ```let``` helper method in RSpec is very frequently (over?)used for creating instance variables that are available between multiple tests. Following this practice can easily lead to having lots of mystery guests showing up and as we’ll address in just a bit , that is not something we need to crash our party—ever! This side effect has gained a bit of a reputation to be possibly causing increased test maintenance and inferior readability throughout your test suite. It sure sounds good because it’s lazily evaluated and aids adhering to the usually zero-defect concept of DRY and all, so it obviously seems too good not to use on a regular basis.

I’m personally in the camp of being rather on the side of repeated setup code for each test than being overly DRY, obscure or cryptic in the test suite. I’d go always for more readablility in my tests. The test method should make clear the cause and effect of its involved pieces—defining parts of it possibly far away is not in your best interest here. If you need to extract stuff, expressive methods that encapsulate that knowledge are often a better bet. It also helps to create the setup for each test that you actually need and not cause slow tests because you have more data than needed for the individual tests methods. Good old variables, methods and classes are often all you need to provide faster, more stable and easier to read tests.


## Fixtures

ActiveRecord database fixtures are a great examples of having tons of Mystery Guests in your test suite. In the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up test data in your application. They played an important role and helped move the industry forward. Nowadays they have a reasonably bad rep though. The hash-like structure sure looks like handy and easy to use. You can reference other nodes within each other if you want to simulate associations from your models. But I think its fair to say that that’s where the music stops and many say their pain started. For data sets that are a bit more involved, YAML fixtures are difficult to maintain and hard to change without affecting other tests. I mean you can make them work of course—after all developers used them plenty in the past—but many people agreed that the price to pay for managing fixtures is just a bit stingy.

To avoid breaking your test data when the inevitable changes occur, developers where happy to adopt newer strategies that offered more flexibility and dynamic behaviour. That’s where Factory Girl came in and kissed the YAML days goodbye. Another issue is the heavy dependency between the test and the .yml fixture file. Since the fixtures are defined in a separte .yml file, mystery guests are also a major pain waiting to bite you. Factory Girl let’s you avoid that by creating objects relevant to the tests inline—and only with the data needed for that specific case. The motto is, only define the bare minimum in your factory definitions and add the rest in a test-by-test basis. I think this approach is more than pretty reasonable and was one major cause why Factory Girl was so well received by the community. Complexity is a fast growing enemy that YAML fixtures are hardly equiped to take on effectively. In some way I think of fixtures also as ```let``` on steroids. You are not only placing them even further away—being in a separate file and all—you are also potentially preloading way more fixtures than you might actually need. RIP!

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




We want to avoid distracting people who will read our tests or even worse, confuse them. That is openeing the door for bugs but can also be expensive cause it can cost valuable time to be billed. When you create your tests, try hard not to override things. It does not aid in creating clarity, it can lead to subtle, time-consuming bugs and it does not affect the aspect of documenting your code positively. Mutating data more than absolutely necessary—when you test for a mutation for example—is also worth keeping in mind. Again, this helps avoiding sending other developers or your future self on  wild goose chases.

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

+ Let
+ Mystery Guest
+ Slow Tests
+ Obscure Tests
+ RSpec DSL Puzzle
+ Brittle Tests
+ Fixtures
+ Factories
+ Uncertainty of Time???

## Let

The ```let``` helper method in RSpec is very frequently used for creating instance variables that are available between multiple tests. Following this practice can easily lead to having lots of mystery guests showing up—see below—which is not something we need to have crashing our party! This particular side effect of ```let``` has gained a bit of a reputation to be possibly causing increased test maintenance and inferior readability throughout your test suite. It sure sounds good because it’s lazily evaluated and aids adhering to the usually zero-defect concept of DRY and all. Therefore it obviously seems too good not to use on a regular basis. Its close cousin ```subject``` should also be avoided most of the time.

It gets worse when you start nesting these things. An all-time favorite are let statements that are plastered all over nested ```describe``` blocks. I think it’s not unfair to call this a recipe for hanging yourself—quickly. More limited scope is generally easier to understand and follow. We don’t want to build a house of cards with semi-global let fixtures that obscure understanding and increase chances of breaking related tests. The odds of crafting quality code are stacked against us with this approach. Extracting common object setup is also easier to do via plain old ruby methods or even classes if needed.

This ```let``` creature is a widely shared fixture which you will often need to decipher first before you know exactly why this object is showing up in your test. Also going back and forth to understand what exactly they are made of and which relationships they have via associations can be a time consuming pain. The clarity of these details when these objects are setup directly in the test scenario usually help a lot to tell other developers all they need to work with this particular part of your test suite—don’t forget your future self! In a world where you never have to revisit particular tests and even never refactor parts of your test suite that might not matter as much—but that is a pipe dream for now!

We want to have as few collaborators and as little data as possible for each test. ```let``` works not in your favor on that front as well. These let fixtures can amass a lot of attributes and methods that make them way too big as well. If you start going down the let road, you will often end up with pretty big objects that try to make a lot of tests happy at the same time. Sure you can create lots of variations of these ```let``` thingies but that makes the whole idea of them a bit irrelevant I think. Why not go one step further, avoid let and rely on Ruby without RSpec DSL magic?

I’m personally in the camp of being rather on the side of repeated setup code for each test than being overly DRY, obscure or cryptic in the test suite. I’d go always for more readability in my tests. The test method should make clear the cause and effect of its involved pieces—defining object collaborators that are possibly far away in your code is not in your best interest. If you need to extract stuff, use expressive methods that encapsulate that knowledge. These are pretty much always a save bet. They can also supply the setup for each test that you actually need and not cause slow tests because you have more data involved than actually needed for the individual test methods. Good old variables, methods and classes are often all you need to provide faster, stable tests that are easier to read. 

### Mystery Guest

Mystery Guests are RSpec DSL Puzzles really. For a while the various objects defined via RSpec DSL ```let``` are not that hard to keep in check but soon when the test suite grows, you invite a lot of mysterious guests into your specs. This gives your future self and others unnecessary context puzzles to solve. The result will be obscure tests that require you to go into full Sherlock Holmes mode. I guess that sounds way more fun than it is. Bottom line, it’s a waste of everybody’s time.

Mystery Guests pose two problematic questions:

+ Where is this object coming from?
+ What exactly is it composed of?

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

This describe block lacks clarity and context. What agent is involved and what mission are we talking about here? This forces developers to go hunting for objects that are suddenly popping up in your tests. Classic example of mystery guests showing up uninvited to your party. When we have lots of code between the relevant test and the origin of these objects, you increase the chances of obscuring what going on in our tests.


The solution is quite easy: You need fresh “fixtures” and build local versions of the objects with exactly the data that you need—and not more than that! Factory Girl is a good choice for handling this. This approach can be considered more verbose and you might be duplicating stuff sometimes—extracting stuff into a method is often a good idea—but it’s a lot more expressive and keeps tests focused while providing context.

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

The example above builds all the objects needed for our tests in the actual test case and provides all the context wanted. The developer can stay focused on a particular test case and does not need to “download” another—possibly totally unrelated—test case for dealing with the situation at hand. No more obscurity!

Yes, you are right, this approach means that we are not achieving the lowest level of duplication possible, but clarity in these cases is mucn more important for the quality of your test suite and therefore for the robustness of your project. The speed in which you can effectively apply changes to your tests plays also a role in that regard. Another important aspect of testing is that your test suite can not only function as documentation but absolutely should! Zero duplication is not a goal that has a positive effect for specs documenting your app. Keeping unnecessary duplication in check is nevertheless an important goal to not loose sight of though—balance is king here!  

Below is another example that tries to setup everything you need locally in the test but also fails because it’s not telling us the full story.

``` ruby

context "agent status" do
  it "returns the status of the mission’s agent" do
    007 = build_stubbed(:agent)
    mission = build_stubbed(:mission, agent: 007)

    expect(mission.agent_status).to eq(007.status)
  end
end

```

We are creating a generic agent. How do we know it’s 007? We are also testing for the agent’s status but this one is also nowhere to be found—neither in the setup nor explicitly during the verify phase in our ```expect``` statement. The relationship between the ```007.status``` and the mission status could be confusing since it’s coming out of nowhere really.

``` ruby

context "agent status" do
  it "returns the status of the mission’s agent" do
    007 = build_stubbed(:agent, name: 'James Bond', status: 'Missing in action'))
    mission = build_stubbed(:mission, agent: 007)

    expect(mission.agent_status).to eq('James Bond: Missing in action')
  end
end

```

## Slow Tests

So, you have started to get into Test-Driven-Development and you started to appreciate what if offers. Kudos, this is great! I’m sure, neither the decision to do it nor the learning curve to get there were exactly a piece of cake. But what often happens after this initial  step is that you try hard at having full test coverage and you start to realize that something is off when the speed of your specs start to annoy you. Why is your test suite getting slower and slower although you think you are doing all the right things? Feeling a bit punished for writing tests? Slow tests suck—big time! There are a couple of problems with them. The most important issue is that slow tests lead to skipping tests in the long run. Once you are at a point where your test suite takes forever to finish, you will be much more willing to think to yourself: ‘Screw this, I’ll run them later! I got better things to do than waiting for this stuff to finish.” And you are absolutely right, you have better things to do.

The thing is, slow tests are more likely to welcome in compromises in the quality of your code than maybe obvious at first. Slow tests also fuel people’s arguments against TDD—unfairly so I think. I don’t even want to know what non-technical product managers have to say if you regularly have to step outside for a nice long coffee break just to run your test suite before you can continue your work. Let’s not go down that road! When you only need little time to exercise your tests and as a result get super quick feedback cycles for developing each step of new features, practicing TDD becomes a lot more attractive and less of an argument. With a little bit of work and care along the way, we can avoid slow mo tests quite effectively. Slow tests are also a killer for getting into the “zone”. If you get taken out of the flow this frequently in your process, the quality of your overall work might also suffer by having to wait for slow tests to return from an expensive round trip. You want to get as much “in-the-zone time” as possible—unbearably slow tests are major flow killers.

Another issue worth mentioning in this context is that this might lead to having tests that cover your code, but because you won’t take time to finish exercising the whole suite, or write tests after the fact, your apps’ design won’t be driven by tests anymore. If you are not on the Test-Driven train this might not bother you much, but for TDD folks that aspect is essential to testing and should not be neglected. Bottom line, the faster your tests, the more you will be willing to exercise them—which is the best way to design apps as well as to catch bugs early and often. What can we do to speed tests? There are two speeds that are important here:

+ The speed in which your tests can really execute your suite.
+ The speed for getting feedback from your test suite to design your app.

### Avoid writing to the database as much as you can.

That does not mean that you should avoid it all costs. Often you don’t need to write tests that exercise the database and you can trim off a lot of time that your tests need to run. Using just ```new``` to instantiate an object is often sufficient test setups. Faking out objects that are not directly under test is another viable option. Creating test doubles for example is a nice way to make your tests faster while keeping the collaborating objects you need for your setup super focused and lightweight. [Factory Girl](https://github.com/thoughtbot/factory_girl) also gives you various options to smartly “create” your test data. But sometimes there is no way around to saving to the database (which is a lot less often than you might expect) and this is exactly where you should draw the line. Any other time, avoid it like hell and your test suite will stay fast and agile. In that regard you should also aim for a minimal amount of dependency, which means the minimal amount of objects that you need collaborating to get your tests to pass—while saving as less as possible to the database along the way. Stubbing out objects—that are mere collaborators and not directly under test—often also make your setup easier to digest and simpler to create. A nice speed boost overall with very little effort.

### Build your tests with the testing pyramid in mind.

This means that you want to have a majority of unit tests at the bottom of this hierarchy—which all focus on very specific parts of your application in isolation—and the smallest amount of integration tests at the top of this pyramid. Integration tests simulate a user going through your system while interacting with a bunch of components that are exercised around the same time. They are easy to write but not so easy to maintain—and the speed losses are not worth going the easy route. Integration tests are pretty much the opposite of unit tests in regard to being high level and sucking in a lot of components that you need to setup in your tests—which is one major reason why they are slower than unit tests. I guess this makes it clear why they should be at the top of your testing pyramid to avoid significant speed losses. Another important issue here is that you want to have as little overlap between these two test categories as possible—you ideally want to test things only once after all. You can’t expect to have perfect separation, but aiming for as little as possible is a reasonable and achievable goal.

In contrast to unit tests, you want to test as few details as possible with integration tests. The inner mechanics should already be covered by extensive unit tests. Focus instead only on the most essential parts that the interactions need to be capable of exercising! The other main reason is that a webdriver needs to simulate going through a browser and interacting with a page. This approach fakes out nothing or very little, saves the stuff to the database and really goes through the UI. That’s also one reason they can be called acceptance tests because these tests try to simulate a real user experience. This is another major speed bump that you want to exercise as little as possible. If you have a ton of these tests—I guess more than 10% from your overall number of tests—you should slow down and reduce that number to the minimum amount possible. Also, keep in mind that sometimes you don’t need to exercise the whole app—a smaller, focused view test often does the trick as well. You will be much faster if you rewrite a couple of your integration tests that just test a little bit of logic that does not necessitate a full integration check. But don’t get into writing a ton of them either, they offer the least bang for the buck. That being said, intergration tests are vital to the quality of your tests and you need to find a balance of being too stingy applying them and not having too much of them around.

### Getting feedback from your app / tests fast.

Quick feedback and fast iteration cycles are key to designing your objects. Once you start to avoid running these tests frequently you are loosing this advantage—which is a big aid for designing objects. Don’t wait until your Continuous Intergration service of choice kicks in to test your whole application. So what’s a magic number we should keep in mind when running tests? Well, different people will tell you different benchmarks for this. I think that staying under 30 secons is a very reasonable number that makes it very likely to exercise a full test on a regular basis. If you leave that benchmark more and more behind, some refactoring might be in order. It will be worth it and it will make you feel much more comfortable because you can check in more regularly. You will most likely move forward a lot faster too that way.

You want that dialog with your tests to be as fast as possible. Tightening this feedback cycle by using an editor that can also exercise your tests is not to be underestimated. Switching back and forth between your editor and your terminal is NOT the best solution to handle this. This gets old very quickly. If you like using Vim, you have one more reason to invest some time to become more efficient at using your editor. Lots of handy tools available for Vim peeps. I remember that Sublime Text also offers to run tests from within the editor but other than that, you need to do a little bit of research to find out what your editor of choice is capable of in that regard. The argument that you will hear frequently from TDD enthusiasts is that you don’t want to leave your editor because overall, you will be spending too much time doing that. You want to stay much more in the zone and not lose train of thought when you can do this sorta thing via a fast shortcut from inside your code editor. 

Another thing to note is that you also want to be able to slice the tests that you want to run. If you don’t need to run the whole file, it’s nice to run a single test or a block that focuses just on what you need to get feedback on right now. Having shortcuts that help you run single tests, single files or just the last test again saves you a ton of time and keeps you in the zone—not to mention the high degree of convenience and feeling super dandy cool as well. It’s just amazing how awesome coding tools can be sometimes.

On last thing for the road. Use a preloader like [Spring](https://github.com/rails/spring). You will be surprised how much time you can shave off when you don’t have to load Rails for every test run. Your app will run in the background and does not need to boot all the time. DO it!

## Fixtures

I’m not sure if fixtures are still an issue for newbies coming to Ruby / Rails land. In case nobody instructed you about them, I’ll try to get you up to speed in a jiffy on these dreaded things. ActiveRecord database fixtures are great examples of having tons of Mystery Guests in your test suite. In the early days of Rails and Ruby TDD, YAML fixtures were the de facto standard for setting up test data in your application. They played an important role and helped move the industry forward. Nowadays, they have a reasonably bad rep though. The hash-like structure sure looks handy and easy to use. You can even reference other nodes if you want to simulate associations from your models. But that’s where the music stops and many say their pain begins. For data sets that are a bit more involved, YAML fixtures are difficult to maintain and hard to change without affecting other tests. I mean you can make them work of course—after all developers used them plenty in the past—but tons of developers will agree that the price to pay for managing fixtures is just a bit stingy.

One scenario we would definitely want to avoid is changing little details on an existing fixture and causing tons of tests to fail. If these failing tests are unrelated, the situation is even worse—a good example of tests being too brittle. In order to “protect” existing tests from this scenario, this can also lead to growing your fixture set beyond any reasonable size—being DRY with fixtures is most likely not on the table anymore at that point. To avoid breaking your test data when the inevitable changes occur, developers where happy to adopt newer strategies that offered more flexibility and dynamic behaviour. That’s where [Factory Girl](https://github.com/thoughtbot/factory_girl) came in and kissed the YAML days goodbye. Another issue is the heavy dependency between the test and the .yml fixture file. Since the fixtures are defined in a separte .yml file, mystery guests are also a major pain waiting to bite you due to being obscure. Did I mention that fixtures are imported into the test database without running through any validations and don’t adhere to the Active Record life cycle? Yeah, that’s not awesome, from whatever angle you wanna look at it! 

Factory Girl let’s you avoid that by creating objects relevant to the tests inline—and only with the data needed for that specific case. The motto is, only define the bare minimum in your factory definitions and add the rest on a test-by-test basis. Locally (in your tests) overriding default values defined in your factories is a much better approach than having tons of fixture unicorns waiting to be outdated in a fixture file—this is more scalable too. Factory Girl gives you plenty of tools to create all the data you need—as nuanced as you like—but also provides you tons of ammo to stay DRY where needed. The pros and cons are nicely balanced with this library I think. Not dealing with validations is also not a cause of concern anymore. I think using the factory pattern for test data is more than pretty reasonable and was one major cause why Factory Girl was so well received by the community. Complexity is a fast growing enemy that YAML fixtures are hardly equipped to take on effectively. In some way I think of fixtures as ```let``` on steroids. You are not only placing them even further away—being in a separate file and all—you are also potentially preloading way more fixtures than you might actually need. RIP!

## Brittle Tests

If changes in your specs lead to seemingly unrelated failures in other tests, you are likely looking at a test suite that has become fragile due to causes mentioned above. These often puzzle-like, mystery guest infested tests easily lead to an unstable house of cards. When objects necessary for tests are defined “far away” from the actual test scenario, it’s not that hard to overlook the relationships that these objects have with their tests. When code gets deleted, adjusted or simply the setup object in question gets accidentally overridden—unaware how this could influence other tests around—failing tests are not a rare encounter. They easily appear like totally unrelated failures. I think it’s fair to include such scenarios into the category of tightly coupled code.

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

In this scenario we have clearly modified locally the objects’s state defined in our setup. The ```agent``` in question is now a CIA operative and has a different name. ```mission``` comes again out of nowhere as well. Nasty stuff really. No surprise when other tests that rely on a different version of ```agent``` start to blow up. Let’s get rid of the ```let``` nonsense and build the objects we need again right where we test them—with only the attributes we need for the test case of course. 
 
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

It is important to understand how objects are related—ideally with the minimum amount of setup code. You don’t want to send other developers on a wild goose chase to figure this stuff out when they stumble over your code. If it’s super hard to get a grasp quickly and a new feature needed to be implemented yesterday, these puzzles can not expect the highest priority. This in turn often means that new stuff get’s developed on top of that unclear context—which is a brittle basis for going forward and also super inviting to bugs down the road.

### Data Attributes

A final useful tip for avoiding brittle tests is to use data attributes in your HTML tags. Just do yourself a favor and use them—you can thank me later. This lets you decouple the needed elements under test from the styling information that your designers might touch frequently or change without your involvement. If you hard code a class like ```class='mission-wrapper'``` in your test and a smart designer decides to change this poor name, your test will be affected unnecessarily. And the designer is not to blame of course. How in the world would she know that this affects part of your test suite—very unlikely at least. 

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

We expect to see some HTML element on a page and marked it with  a ```data-role```. Designers have no reason to touch that and you are protected against brittle tests that happen due to changes on the styling side of things. Pretty effective and useful strategy that basically costs you nothing in return. The only thing that might be necessary is to have a short conversation with designers. Piece of cake!

## Final Thoughts

We want to avoid distracting people who will read our tests or even worse, confuse them. That is opening the door for bugs but can also be expensive because it can cost valuable time and brain power. When you create your tests, try hard not to override things—it does not aid in creating clarity. More likely it will lead to subtle, time-consuming bugs and won’t affect the aspect of documenting your code positively. This creates an unnecessary burden we can avoid. Mutating test data more than absolutely necessary is also worth being a bit paranoid about. Keep it as simple as possible! This really helps avoiding sending other developers or your future self on wild goose chases.

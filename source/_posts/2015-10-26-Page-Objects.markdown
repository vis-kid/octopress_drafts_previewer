---
layout: post
title: Ruby Page Objects for Capybara Connoisseurs
date: 2015-10-26 04:29:10 +0100
comments: true
sharing: true
published: true 
categories: [Ruby, Rails, thoughtbot, TDD, BDD, Test-Driven-Design, RSpec, Factory Girl]
---

{% img /images/Page_Objects/capybara_bigger.png %}


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

I’ll give you the short answer first. Its a design pattern to encapsulate markup and page interactions—specifially to refactor your feature specs. Its a combination of a two very common refactoring techniques: *Extract class* and  *Extract method*—which don’t have to happen at the same time because you can gradually build up to extracting a whole class via a new Page Object. 

This technique offers you to write high-level feature specs that are very expressive and DRY. In a way they are acceptance tests with application language. You might ask, aren’t specs written with Capybara already high-level and expressive? Sure, for a developers who write codes on a daily basis, Capybara specs read just fine. Are the DRY out of the box? Certainly not!

``` ruby
feature 'M assigns a mission' do
  scenario 'they see the foobar on the page' do
    visit new_mission_path
    
    click_on 'Create Mission' 
    fill_in 'Mission Name',  with: 'Top Secret Mission'
    fill_in 'Agent', with: 'James Bond'
    click_button 'Create Mission'

    expect(page).to have_css 'li.mission-name', 'Top Secret Mission'
  end
end
```

When you look at this example of such a feature spec, where do you see opportunities to make this read better and how could you extract information to avoid duplication? Also, is this high-level enough for easy modeling of user stories and for non-technical stakeholders to understand? In my mind, there are a couple of ways to improve this and to make everybody happy—developers who can avoid fiddling with the details of interacting with the DOM while applying OOP and other non-coding team members having no trouble jumping between user stories and these tests. The last point is nice to have for sure, but the most important benefits come mostly from making your DOM-interacting specs more robust.

Encapsulation is the key concept with Page Objects. When you write your feature specs you’ll benefit from a strategy to extract the behaviour that is driving through a test flow. For quality code you want to capture the interactions with particular sets of elements on your pages—especially if you stumble upon repeating patterns. As your application grows, you want / need an approach that avoids spreading that logic all over your specs. 

”Well isn’t that overkill? Capybara reads just fine!” you say? Ask yourself:
Why wouldn’t you have all the HTML implementation details in one place while having more stable tests? Why shouldn’t UI interaction tests have the same quality as tests for application code? Do you really wanna stop there? Due to everyday changes, Capybara selectors introduce possible breakpoints. Let’s say a designer wants to change the text on a button. No biggie right, but do you want to adapt to that change in one central wrapper for that element in your specs or do you prefer to do that all over the place? I thought so!

There are many refactorings possible for your feature specs but Page Objects offer the cleanest abstractions for encapsulating user facing behaviour for pages or more complex flows. You don’t have to simulate the whole page(s) though—focus on the essential bits that are necessary for user flows. No need to overdo it!

+ ### Acceptance Tests / Feature Specs

Before we move on to the heart of the matter, I’d like to take a step back for people new to the whole testing business and clear up some of the lingo that is important in this context. People more familiar with TDD won’t miss much if they skip ahead.

What are we talking about here? Acceptance testing usually comes in at a later stage of projects to evaluate if you have been building something of value for your users, product owner, or whatever stakeholder. These tests are usually run by customers or your users. Its sort of a check if the requirements are being met or not. There is something like a pyramid for all sorts of testing layers and acceptance tests are near the very top. Because this process includes often non-technical folks, a high-level language for writing these tests is a valuable asset to communicate back and forth.

Feature specs on the other hand, are a bit lower in the testing food chain. A lot more high-level than unit tests which focus on the technial details and business logic of your models, feature specs describe flows on and in between your pages. Tools like [Capybara](http://jnicklas.github.io/capybara/) help you to avoid doing this manually—meaning that you rarely have to open your browser to test stuff manually. With these kinds of tests we like to automate these tasks as much as possible and test drive the interaction through the browser while writing assertions against pages. Btw, you don’t use **get**, **put**, **post** or **delete** like with request specs. 

Feature specs are very similar to acceptance tests—sometimes I feel the differences are too blurry to really care about the terminology. You write tests that exercise your whole application which often involves a multi-step flow of user actions. These tests show if your components work in harmony when they are brought together. In Ruby land, they are the main protagonist when we’re dealing with Page Objects. Feature specs themselves are already very expressive but they can be optimized and cleaned up by extracting their data, behaviour and markup into separate classes. I felt clearing up this blurry terminology will help you see that having Page Objects is a bit like doing acceptance level testing while writing feature specs.

### Capybara

Maybe we should go over this really quickly as well. This library describes itself as an “Acceptance test framework for web applications”. You can simulate user interactions with your pages via a very powerful and convenient domain-specific language. In my personal opinion, RSpec paired with Capybara offers atm the best way to write your feature specs. It lets you visit pages, fill in forms, click on links and buttons, look for markup on your pages and you are able to easily combine all kinds of these commands to interact with your pages. 

You basically can avoid opening the browser yourself to test this stuff manually most of the time—which is not only less elegant but also a lot more time consuming and error-prone. Without this tool, the process of “Outside-in-testing”–you drive your code from high-level tests down into your unit-level tests—would be a lot more painful and possibly therefore more neglected. In other words you start writing these feature tests which are based on your user stories and from there you go down the rabbit whole until your unit tests provide the coverage you need. After that, when your tests are green of course, the game starts anew and you go back up to continue with a fresh feature test.

+ ### How?

Let’s look at a simple example of a feature spec that lets M create classified missions that can be completed. In the markup, you have a list of missions and a successful completion creates an addtional **li** with the class **completed**. Straightforward stuff right?

**spec/features/m_creates_a_mission_spec.rb**

``` ruby
require 'rails_helper'

feature 'M creates mission' do
  scenario 'successfully' do
    sign_in_as 'M@mi6.com'
    
    create_classified_mission 'Project Moonraker'

    expect(page).to have_css 'ul.missions', text: 'Project Moonraker'
  end

  def create_classified_mission(mission_name)
    visit missions_path
    click_on 'Create Mission' 
    fill_in  'Mission Name', with: mission_name
    click_on 'Submit'
  end

  def sign_in_as(email)
    visit root_path
    fill_in  'Email', with: email
    click_on 'Submit'
  end
end
```

**spec/features/agent_completes_a_mission_spec.rb**

``` ruby
require 'rails_helper'

feature 'Agent completes mission' do
  scenario 'successfully' do
    sign_in_as 'M@mi6.com'
    
    create_classified_mission 'Project Moonraker'

    within "li:contains('Project Moonraker')" do
      click_on 'Mission completed'
    end

    expect(page).to have_css 'ul.missions li.completed', text: 'Project Moonraker'
  end

  def create_classified_mission(mission_name)
    visit missions_path
    click_on 'Create Mission' 
    fill_in  'Mission Name', with: mission_name
    click_on 'Submit'
  end

  def sign_in_as(email)
    visit root_path
    fill_in  'Email', with: email
    click_on 'Submit'
  end
end
```


Although there are other ways of course to deal with stuff like **sign_in_as** and **create_classified_mission**, its easy to see how fast these things can start to suck. UI related specs often don’t get the OO treatment they need I think. They have the reputation of providing too little bang for the buck and of course developers are not most fond of times when they have to touch the markup much. In my mind, that makes it even more important to DRY these specs up and make it fun to deal with them by throwing in a couple of Ruby classes.

Let’s do a little magic trick where I hide the Page Objects implementation for now and only show you the end result applied to the feature specs above:

``` ruby
require 'rails_helper'

feature 'M creates mission' do
  scenario 'successfully' do
    sign_in_as 'M@mi6.com'
    mission_page = Pages::Missions.new

    mission_page.create_classified_mission 'Project Moonraker'

    expect(page).to have_mission_with_title 'Project Moonraker'
  end
end
```

**spec/features/agent_completes_a_mission_spec.rb**

``` ruby
require 'rails_helper'

feature 'Agent completes mission' do
  scenario 'successfully' do
    sign_in_as 'M@mi6.com'
    mission_page = Pages::Missions.new

    mission_page.create_classified_mission title: 'Project Moonraker'
    mission_page.mark_complete 'Project Moonraker'
    
    expect(page).to have_completed_mission_with_title 'Project Moonraker'
  end
end
```

Doesn’t this read a whole lot better? You basically create expressive wrapper methods on your Page Objects that deal with high-level concepts—instead of fiddling everywhere with the intestines of your markup all the time. You basically extract methods that do this kind of dirty work and that way [**Shotgun surgery**](https://en.wikipedia.org/wiki/Shotgun_surgery) is not your problem anymore. Put differently, you abstract away a lot of the noisy, down in the weeds DOM interacting code. Let’s take a look at the implementation: 

**specs/support/features/pages/missions.rb**

``` ruby
module Pages
  class Missions
    include Capybara::DSL

    def create_classified_mission(mission_name)
      visit missions_path
      click_on 'Create Mission' 
      fill_in  'Mission Name', with: mission_name
      click_on 'Submit'
    end

    def mark_complete(mission_name)
      within "li:contains('#{mission_name}')" do
      click_on 'Mission completed'
    end

    def has_mission_with_name?(mission_name)
      mission_list.has_css? 'li', text: mission_name
    end

    def has_completed_mission_with_name?(mission_name)
      mission_list.has_css? 'li.completed', text: mission_name
    end
  end

  private

  def mission_list do
    find('ul.missions')
  end
end
```

What you see is plain old Ruby object—Page Objects are in essence very simple classes really. Normally you don’t instantiate Page Objects with data and you create mostly a language via the API that a user or a non-technical stakeholder on a team might use. When you think about naming your methods, I think its good advice to ask yourself the question: How would a user describe the flow or the action taken?

Btw, **spec/support/features/pages** is a solid place to store your Page Objects. I should also probably add that without including Capybara the music stops pretty fast.

``` ruby
include Capybara::DSL
```

If you’re curious what happened to the **sign_in_as** method, I extracted it into a module at **spec/support/sign_in_helper.rb** and told RSpec to include that module. This has nothing to do with Page Objects, but its a makes more sense to store functionality like sign in more globally accessible than via Page Objects.

``` ruby
module SignInHelper
  def sign_in_as(email)
    visit root_path
    fill_in 'Email', with: email
    click_on 'Submit'
	end
end

RSpec.configure do |config|
  config.include SignInHelper
end
```

Overall, its easy to see that we succeed with hiding away the Capybara specifics—like finding elements, clicking links etc—you need for your tests so that you can focus on the functionality and less on the actual structure of the markup which is now encapsulated in a Page Object—the DOM structure should be the least of your concerns when you test something as high-level as feature specs.




You probably wonder how these custom matchers work: 

``` ruby
def has_mission_with_name?(mission_name)
  ...
end

def has_completed_mission_with_name?(mission_name)
  ...
end


expect(page).to have_mission_with_name
expect(page).to have_completed_mission_with_name
```

RSpec generates these custom matchers based on predicate methods on your Page Objects. RSpec converts them by removing the **?** and changes **has** to **have**. Boom, matchers from scratch without much fuzz! A bit magic, I’ll give you that, but the good kind of wizardry I’d say.

### Attention!

Setup stuff like factory data belongs in the specs and not in Page Objects. Also assertions are probably better placed outside of your Page Objects to achieve a separation of concerns. There are two differnt perspectives on the topic:

Advocates for putting assertions into Page Objects say it helps with avoiding duplication of assertions. You can provide better error messages and achieve a better “Tell, Don’t Ask” style. On the other hand, advocates for assertion-free Page Objects argue that its better to not mix responsibilities. Providing access to page data and assertion logic are two separate concerns and lead to bloated Page Objects when mixed. Page Object’s responsibility is access to the state of pages and assertion logic belongs to specs.

+ ### Page Objects Types

**Components** represent the smallest units and are more focused—like a form object for example. 

**Pages** combine more of these components and are abstractions of a full page.

**Experiences**, as you guessed by now, span the whole flow across potentially many different pages. They are more high-level. They focus on the flow the user experiences while they interact with various pages. A checkout flow which has a couple of steps is a good example to think about this.

+ ### When & Why?

Its a good idea to apply this design pattern a little bit later in a project’s life cycle—when you have amassed a little bit of complexity in your feature specs and when you can identify repeating patterns like DOM structures or other commonalities that are consistent on your pages. So you probably shouldn’t start writing Page Objects right away. You approach these refactorings gradually when the complexity and size of your application / tests grow. Duplications and refactorings that need a better home through Page Objects will be easier to spot over time. My recommendation is to start with extracting methods in your feature specs locally first. Once they’ll hit critical mass, they’ll look like obvious candidates for further extraction and most of them will probably fit the profile for Page Objects. Start small, premature optimization leaves nasty bite marks! 

+ ### Final Thoughts

Page Objects provide you the opportunity to write clearer specs that read better and are overall a lot more expressive because they are more high-level. Besides that, they offer a nice abstraction for everybody who likes to write OO code. They hide the specifics of the DOM and also enable you to have private methods that do the dirty work while being unexposed to the public API. Extracted methods in your feature specs don’t offer the same luxury. The API of Page Objects don’t need to share the nitty-gritty Capybare details

For all the scenarios when design implementations change, your descriptions of how your app should work doesn’t need to change when you use Page Objects—your feature specs are more focused with user level interactions and don’t care so much about the specifics of the DOM implementations. Since change is inevitable, Page Objects become critical when applications grow and also aid the understanding when the sheer size of the application means drastically increased complexity.

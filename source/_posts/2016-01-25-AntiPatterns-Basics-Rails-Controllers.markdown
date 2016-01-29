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

## FAT Controllers

Well, “fat models, skinny controllers”, right? In case you haven’t read the previous AntiPattern articles, I should mention that aiming for models and controllers that stay skinny is a better guideline—no matter what. All that excess fat is not good for your projects—“skinny everything” makes much more sense. (Maybe I should disclaim that I’m not associated with the fashion industry in any way and don’t want to repeat the impression that you can’t be considered beautiful without fitting a certain type of imaginary body). As with models, you want controllers that have single responsibilities. Controllers should be dumb really, managing traffic and not much else. We want to make our templates as dumb as possible through presenters.

It is also important that you do not stray much from RESTful controller actions. Sure, every once in a while it can make sense to have additional methods in there but most of the time, you should feel a little uneasy having them around. Controllers tend to get fat when they amass business logic that actually belongs into models or when inexperienced developers don’t make use of Rails’ conventions. You won’t be the last trying to reinvent the wheel and you certainly won’t be the last. Don’t feel bad about it, probably most of us have been there, but as a craftsman, you really should put in the time to know the conventions, benefits and limitations of the frameworks you use—at least for commercial purposes where somebody pays for your expertise.

Since controllers are in charge of the flow of your application as well as for gathering information that your views need, they already have a pretty important responsibility. They really do not need added complexity from the realm of your models. Controllers are closely working with your views to display the data provided by the model layer. Their relationship is tighter than with models. The model layer can potentially be developed much more independent from the others. The good thing about that is that a clean controller layer often has a positive effect on how tidy your views can be.

What I want to get across is that fat controllers are super common in Rails land—especially among beginners and inexperienced developers—and with a little bit of love and care we can avoid that easily in the future. The first step is straightforward. Ask yourself if a controller grows in size if the complexity comes from added business logic. If so, find a way to move it to the model layer where you have the added benefit of having a better home for testing complex code.

### Presenters

To follow the above recommendation of moving aquired controller logic to models, presenters are a handy technique. They can simulate a model while combining a couple of loosely related attributes together. They can be useful for keeping controllers slim and sexy. They are also good at keeping logic nastiness out of your views. Pretty good deal!

Presenters can simulate a model which represents the state that your view needs and combines the attributes the controller serves to your presentation layer. Sometimes a controller is in charge of creating multiple models simultaneously and we want to avoid is handling multiple instance variables in there. Why is this important? Because it helps us to keep the maintainability of our apps in check. The presenter aggregates behaviour and attributes which makes it easy for our controllers to focus on small, dead-simple jobs with a single object. Also, formatting data in your view or other similar small functions are frequent jobs that often occur. Having this contained in a presenter is not only great for clean views but also for having a dedicated place that makes testing this  behaviour straightforward—classes are easy to test.

The most commonly cited scenario is some sort of form that inputs information for various different models. Like a new user account that also has input fields for credit cards and addresses or something or something. Since these parts of your application tend to be very important ones, it is definitely a good idea to keep things tidy while having the best possible option available for testing at the same time. In the example below we want to create a simple mission that ```has_one``` ```agent``` and one ```quartermaster```. No rocket science, but it’s a good example to see how quickly things can get out of hands. The controller needs to juggle multiple objects that the view needs in a nested form to tie things together. You will soon see that all of this can be cured with a nice presenter weaving things together in one central class.

``` ruby

class Mission < ActiveRecord::Base
  has_one :agent
  has_one :quartermaster
  accepts_nested_attributes_for :agent, :quartermaster, allow_destroy: true
end

class Agent < ActiveRecord::Base
  belongs_to :mission
end

class Quartermaster < ActiveRecord::Base
  belongs_to :mission
end

```


I’m mentioning the models here just for completeness sake in case you never used ```fields_for``` before—a bit simplified but working. Below is the meat of the matter. 

##### Too many instance variables

``` ruby

class MissionsController < ApplicationController
  def new
    @mission = Mission.new
    @agent = Agent.new
    @quartermaster = Quartermaster.new
  end

  def create
    @mission = Mission.new(mission_params)
    @agent = Agent.new(agent_params)
    @quartermaster = Quartermaster.new(quartermaster_params)

    @mission.agent = @agent
    @mission.quartermaster = @quartermaster

    if @account.save and @agent.save and @quartermaster.save
      flash[:notice] = 'Mission accepted'
      redirect_to @mission
    else
      render :new     
    end
  end

  private

  def mission_params
    params.require(:mission).permit(:mission_name, :objective, :enemy)
  end

  def agent_params
    params.require(:agent).permit(:name, :number, :licence_to_kill)
  end

  def quartermaster_params
    params.require(:quartermaster).permit(:name, :number, :hacker, :expertise, :humor)
  end

end

```

Overall, it’s easy to see that this is heading in the wrong direction. The quickly growing amount of private methods are already piling up way to fast as well. ```agent_parms``` and ```quartermaster_params``` in a ```MissionsController``` does not sound slick to you I hope. A rare sight you think? I’m afraid not. “Single Responsibilities” in controllers truly are a golden guideline. You’ll see why in just a minute.

Even if you squint your eyes, this looks super nasty. And during saving, with validations in place, if we can’t save every object due to some mistake or something, we’ll end up with orphaned objects that nobody wants to deal with. Yikes! Sure we could put this into a ```transaction``` block which successfully completes saving only if all objects are in order, but this is a bit like surfing against the current—also, why do model level stuff like this in the controller really? There are more elegant ways to catch a wave.

In the view we would have an accompanying ```form_for``` for ```@mission``` and the additional ```fields_for``` for ```@agent``` and ```@quartermaster``` of course.

##### Messy Form With Multiple Objects

``` erb

<%= form_for(@mission) do |mission| %>

  <h3>Mission</h3>
  <div class='mission-input'>
    <%= mission.label      :mission_name %>
    <%= mission.text_field :mission_name %>
  </div>

  <div class='mission-input'>
    <%= mission.label      :objective %>
    <%= mission.text_field :objective %>
  </div>

  <div class='mission-input'>
    <%= mission.label      :enemy %>
    <%= mission.text_field :enemy %>
  </div>

  <h3>Agent</h3>
  <%= fields_for @agent do |agent| %>

    <div class='mission-input'>
      <%= agent.label      :name %>
      <%= agent.text_field :name %>
    </div>

    <div class='mission-input'>
      <%= agent.label      :number %>
      <%= agent.text_field :number %>
    </div>

    <div class='mission-input'>
      <%= agent.label     :licence_to_kill %>
      <%= agent.check_box :licence_to_kill %>
    </div>

  <% end %>

  <h3>Quartermaster</h3>
  <%= fields_for @quartermaster do |quartermaster| %>

    <div class='mission-input'>
      <%= quartermaster.label      :name %>
      <%= quartermaster.text_field :name %>
    </div>

    <div class='mission-input'>
      <%= quartermaster.label      :number %>
      <%= quartermaster.text_field :number %>
    </div>

    <div class='mission-input'>
      <%= quartermaster.label     :hacker %>
      <%= quartermaster.check_box :hacker %>
    </div>

    <div class='mission-input'>
      <%= quartermaster.label      :expertise %>
      <%= quartermaster.text_field :expertise %>
    </div>

    <div class='mission-input'>
      <%= quartermaster.label     :humor %>
      <%= quartermaster.check_box :humor %>
    </div>

  <% end %>

  <%= mission.submit %>

<% end %>

```

Sure, this works and all but I would not be too excited to stumble upon this.   ```fields_for``` is cool and all but handling this with OOP is a lot more dope. Here, the presenter will also aid us in having a simpler view because the form will deal with just a single object. Nesting the form becomes unnecessary that way. Testing these kind of scenarios where multiple models come together should be treated with utmost care—the simpler the objects in question, the better the testing experience. Simple really. Presenters operate in your favor on this one. Having these tests potentially tied to the controller is not the best way to approach this. Remember, unit tests are fast and cheap. Let’s do this!

A word of caution. Do not overuse presenters—they should not be your first choice. Usually, the need for a presenter grows over time. For me personally, they are best used when you have data represented by multiple models that need to come together in a single view. Without a presenter, you might more often than not prepare multiple instance variables in your controller for a single view. That alone can make them real fat really quickly. A presenter combines them in a single object that can be served to the view via a single instance variable.

A thing that you should consider and weigh is that presenters add objects to your codebase. At the same time, it also reduces the number of objects a controller needs to deal with and reduces complexity by that. 

Let’s try to keep our controller actions mostly as simple as dealing with the CRUD manipulation of resources—that’s what a controller does for a living and is best equiped to do without muddying the MVC distinctions. As a nice side effect, the level of complexity in your controllers will go way down.


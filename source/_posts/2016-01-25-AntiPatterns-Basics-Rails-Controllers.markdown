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
+ Non-RESTful Controllers
+ Rat’s Nest Rsources

## FAT Controllers

Well, “fat models, skinny controllers”, right? In case you haven’t read the previous AntiPattern articles, I should mention that aiming for models and controllers that stay skinny is a better guideline—no matter what. All that excess fat is not good for your projects—“skinny everything” makes much more sense. (Maybe I should disclaim that I’m not associated with the fashion industry in any way and don’t want to repeat the impression that you can’t be considered beautiful without fitting a certain type of imaginary body). As with models, you want controllers that have single responsibilities. Controllers should be dumb really, managing traffic and not much else. We want to make our templates as dumb as possible through presenters.

It is also important that you do not stray much from RESTful controller actions. Sure, every once in a while it can make sense to have additional methods in there but most of the time, you should feel a little uneasy having them around. Controllers tend to get fat when they amass business logic that actually belongs into models or when inexperienced developers don’t make use of Rails’ conventions. You won’t be the last trying to reinvent the wheel and you certainly won’t be the last. Don’t feel bad about it, probably most of us have been there, but as a craftsman, you really should put in the time to know the conventions, benefits and limitations of the frameworks you use—at least for commercial purposes where somebody pays for your expertise.

Since controllers are in charge of the flow of your application as well as for gathering information that your views need, they already have a pretty important responsibility. They really do not need added complexity from the realm of your models. Controllers are closely working with your views to display the data provided by the model layer. Their relationship is tighter than with models. The model layer can potentially be developed much more independent from the others. The good thing about that is that a clean controller layer often has a positive effect on how tidy your views can be.

What I want to get across is that fat controllers are super common in Rails land—especially among beginners and inexperienced developers—and with a little bit of love and care we can avoid that easily in the future. The first step is straightforward. Ask yourself if a controller grows in size if the complexity comes from added business logic. If so, find a way to move it to the model layer where you have the added benefit of having a better home for testing complex code.

### Presenters

To follow the above recommendation of moving aquired controller logic to models, presenters are a handy technique. They can simulate a model while combining a couple of loosely related attributes together. They can be useful for keeping controllers slim and sexy. They are also good at keeping logic nastiness out of your views. Pretty good deal!

Presenters can simulate a model which represents the state that your view needs and combines the attributes the controller serves to your presentation layer. Sometimes a controller is in charge of creating multiple models simultaneously and we want to avoid is handling multiple instance variables in there. Why is this important? Because it helps us to keep the maintainability of our apps in check. The presenter aggregates behaviour and attributes which makes it easy for our controllers to focus on small, dead-simple jobs with a single object. Also, formatting data in your view or other similar small functions are frequent jobs that often occur. Having this contained in a presenter is not only great for clean views but also for having a dedicated place that makes testing this  behaviour straightforward—classes are easy to test.

The most commonly cited scenario is some sort of form that inputs information for various different models. Like a new user account that also has input fields for credit cards and addresses or something or something. Since these parts of your application tend to be very important ones, it is definitely a good idea to keep things tidy while having the best possible option available for testing at the same time. In the example below we want to create a simple mission that ```has_one``` ```agent``` and one ```quartermaster```. No rocket science, but it’s a good example to see how quickly things can get out of hands. The controller needs to juggle multiple objects that the view needs in a nested form to tie things together. You will soon see that all of this can be cured with a nice presenter weaving things together in one central class.

##### app/models/mission.rb

``` ruby

class Mission < ActiveRecord::Base
  has_one :agent
  has_one :quartermaster
  accepts_nested_attributes_for :agent, :quartermaster, allow_destroy: true

  validates :mission_name, presence: true
  ...

end

class Agent < ActiveRecord::Base
  belongs_to :mission
  validates :name, presence: true
  ...

end

class Quartermaster < ActiveRecord::Base
  belongs_to :mission
  validates :name, presence: true
  ...

end

```


I’m mentioning the models here just for completeness sake in case you never used ```fields_for``` before—a bit simplified but working. Below is the meat of the matter. 

#### Too many instance variables

##### app/controllers/missions_controller.rb

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
      redirect_to missions_path
    else
      flash[:alert] = 'Mission not accepted'
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

### Messy Form With Multiple Objects

##### app/views/missions/new.html.erb

``` erb

<%= form_for(@mission) do |mission| %>

  <h3>Mission</h3>
    <%= mission.label      :mission_name %>
    <%= mission.text_field :mission_name %>

    <%= mission.label      :objective %>
    <%= mission.text_field :objective %>

    <%= mission.label      :enemy %>
    <%= mission.text_field :enemy %>

  <h3>Agent</h3>
  <%= fields_for @agent do |agent| %>
      <%= agent.label      :name %>
      <%= agent.text_field :name %>

      <%= agent.label      :number %>
      <%= agent.text_field :number %>

      <%= agent.label      :licence_to_kill %>
      <%= agent.check_box  :licence_to_kill %>
  <% end %>

  <h3>Quartermaster</h3>
  <%= fields_for @quartermaster do |quartermaster| %>
      <%= quartermaster.label      :name %>
      <%= quartermaster.text_field :name %>

      <%= quartermaster.label      :number %>
      <%= quartermaster.text_field :number %>

      <%= quartermaster.label      :hacker %>
      <%= quartermaster.check_box  :hacker %>

      <%= quartermaster.label      :expertise %>
      <%= quartermaster.text_field :expertise %>

      <%= quartermaster.label      :humor %>
      <%= quartermaster.check_box  :humor %>
  <% end %>

  <%= mission.submit %>
<% end %>

```

Sure, this works but I wouldn’t be too excited to stumble upon this.   ```fields_for``` is cool and all but handling this with OOP is a lot more dope. For such a case, a presenter will also aid us in having a simpler view because the form will deal with just a single object. Nesting the form becomes unnecessary that way. Btw, I left out any wrappers for styling the form to make it easier to digest visually. 

### Form Object Presenter

##### app/views/missions/new.html.erb

``` erb

<%= form_for @mission_presenter, url: missions_path do |mission| %>
  <h3>Mission</h3>
    <%= mission.label      :mission_name %>
    <%= mission.text_field :mission_name %>

    <%= mission.label      :objective %>
    <%= mission.text_field :objective %>

    <%= mission.label      :enemy %>
    <%= mission.text_field :enemy %>

  <h3>Agent</h3>
    <%= mission.label      :agent_name %>
    <%= mission.text_field :agent_name %>

    <%= mission.label      :agent_number %>
    <%= mission.text_field :agent_number %>

    <%= mission.label      :licence_to_kill %>
    <%= mission.check_box  :licence_to_kill %>

  <h3>Quartermaster</h3>
    <%= mission.label      :quartermaster_name %>
    <%= mission.text_field :quartermaster_name %>

    <%= mission.label      :quartermaster_number %>
    <%= mission.text_field :quartermaster_number %>

    <%= mission.label      :hacker %>
    <%= mission.check_box  :hacker %>

    <%= mission.label      :expertise %>
    <%= mission.text_field :expertise %>

    <%= mission.label      :humor %>
    <%= mission.check_box  :humor %>

  <%= mission.submit %>
<% end %>

```

As you can easily see, our view has become much simpler—no nestings and it’s a lot more straightforward this flat. The part you need to be a bit careful is this:

``` erb

<%= form_for @mission_presenter, url: missions_path do |mission| %>

```

You need to provide ```form_for``` with a path via ```url``` so that it can ```post``` the params from this form to their proper controller, here ```MissionsController```. Without that additional argument, Rails would try to find the controller for our presenter object ```@mission_presenter```—in this case ```MissionFormPresentersController```—and blow up without one.

In general, we should try our best to keep controller actions mostly as simple as dealing with the CRUD manipulation of resources—that’s what a controller does for a living and is best equiped to do without muddying the MVC distinctions. As a nice side effect, the level of complexity in your controllers will go way down as well.

##### app/controllers/missions_controller.rb

``` ruby

class MissionsController < ApplicationController

  def new
    @mission_presenter = MissionFormPresenter.new
  end

  def create
    @mission_presenter = MissionFormPresenter.new(mission_params)
    if
      @mission_presenter.save
      flash[:notice] = 'Mission accepted'
      redirect_to missions_path
    else
      flash[:alert] = 'Mission not accepted'
      render :new
    end
  end

  private

  def mission_params
    params.require(:mission_form_presenter).permit(whitelisted)
  end

  def whitelisted
    [:mission_name, :objective, :enemy, :agent_name, :agent_number, :licence_to_kill, :quartermaster_name, :quartermaster_number, :hacker, :expertise, :humor]
  end
end

```

The controller is also a lot easier on the eyes, isn’t it? Much cleaner and pretty much standard controller actions. We are dealing with a single object that has one job. We instantiate a single object, the presenter, and feed it the params as usual.

The only thing that bugged me was sending this long list of whitelisted strong parameters. I extracted them into a method called ```whitelisted``` which just returns an array with the complete list of parameters. Otherwise, ```mission_params``` would have looked like the following—which felt too nasty:

``` ruby

def mission_params
  params.require(:mission_form_presenter).permit(:mission_name,
                                                 :objective, :enemy,
                                                 :agent_name, 
                                                 :agent_number, 
                                                 :licence_to_kill, 
                                                 :quartermaster_name, 
                                                 :quartermaster_number, 
                                                 :hacker, 
                                                 :expertise, 
                                                 :humor)
end

```

In our ```Mission``` model, we now have no need for ```accepts_nested_attributes``` anymore and can get rid of that harmless looking dreaded thing. The ```validates``` method is also irrelevant here because we add this responsibility to our form object. Same goes for our validations on ```Agent``` and ```Quartermaster``` of course. Encapsulating this validation logic directly on the presenter helps us clean our classes. In cases where you  would create these objects independent from each other, validations should stay where they currently are of course.

##### app/models/mission.rb

``` ruby

class Mission < ActiveRecord::Base
  has_one :agent
  has_one :quartermaster
  #accepts_nested_attributes_for :agent, :quartermaster, allow_destroy: true

  #validates :mission_name, presence: true
  ...

end

```

Oh, and a word about the ```:mission_form_presenter``` argument for ```params.require```. Although we named our instance variable for the presenter ```@mission_presenter```, when we use it with ```form_for```, Rails expects the key of the params hash from your form to be named after the object instantiated—not after the name given in a controller. I have seen newbies trip over this several times. That Rails is providing you with cryptic errors in such a case isn’t helping either. If you need a little refresher on params, this is a good place to dig in:

+ [Documentation](http://api.rubyonrails.org/classes/ActionController/Parameters.html)
+ [Free screencast](https://www.youtube.com/watch?v=y57OnWV6dRE)

Now we have a skinny controller with a single responsibility and a flat form for creating multiple objects in parallel. Awesome! How did we achieve all this?  Below is the presenter that does all the work—not implying this class does a lot of work though. We want to have some sort of intermediary model without a database that juggles multiple objects. Take a look at this plain old ruby object (PORO). I think it’s fair to say that it’s not very complicated. ```MissionFormPresenter``` is a form object that now encapsulates what made our controller unnecessarily fat. As a bonus, our view is flat and simple.

##### app/presenters/mission_form_presenter.rb

``` ruby

class MissionFormPresenter
  include ActiveModel::Model

  attr_accessor  :mission_name, :objective, :enemy, :agent_name,
                 :agent_number, :licence_to_kill, :quartermaster_name,
                 :quartermaster_number, :hacker, :expertise, :humor

  validates :mission_name, :agent_name, :quartermaster_name, presence: true

  def save
    ActiveRecord::Base.transaction do
      @mission = Mission.create!(mission_attributes)
      @mission.create_agent!(agent_attributes)
      @mission.create_quartermaster!(quartermaster_attributes)
    end
  end

  private 

  def mission_attributes
    { mission_name: mission_name, objective: objective, enemy: enemy }
  end

  def agent_attributes
    { name: agent_name, number: agent_number, licence_to_kill: licence_to_kill }
  end

  def quartermaster_attributes
    { name: quartermaster_name, number: quartermaster_number, hacker: hacker, expertise: expertise, humor: humor }
  end
end

```

What happens here is that we can aggregate all the info from our form and then we create all the objects we need sequentially. The most important piece happens in our new ```save``` method. First we create the new ```Mission``` object. After that we can create the two objects assoicated with it: ```Agent``` and ```Quartermaster```. Through our ```has_one``` / ```belongs_to``` associations, we can make use of of a ```create_x``` method that adapts to the name of the associated object. For example, if we use ```has_one :agent``` we get a ```create_agent``` method. Easy, right? (FYI, actually we also get a ```build_agent``` method.) I decided to use the version with a bang(!) because it raises a ```ActiveRecord::RecordInvalid``` error if the record is invalid. Wrapped inside a ```transaction``` block, these bang methods take care that no ophaned object gets saved if some validation kicks in. The transaction block will roll back if something goes wrong during save. 

How does this work with the attributes you might ask? We ask Rails for a little bit of love via ```include ActiveModel::Model``` ([API](http://api.rubyonrails.org/classes/ActiveModel/Model.html)). This allows us to initialize objects with a hash of attributes—which is exactly what we do in the controller. After that, we can use our ```attr_accessor``` methods to extract our attributes to instantiate the objects we really need. ```ActiveModel::Model``` further enables us to interact with views and controllers. Among other goodies, you can also use this for validations in such classes. Putting these validations into such dedicated form objects is a good idea for organization and also keeps your models a bit more tidy. Also, I decided to extract the long list of parameters into private methods which feed the objects that get created in ```save```. In such a presenter object, I have little concern of having a couple more private methods lying around. Why not? Feels cleaner!


Testing these kind of scenarios where multiple models come together should be treated with utmost care—the simpler the objects in question, the better the testing experience. No rocket science really. Presenters operate in your favor on this one. Having these tests potentially tied to the controller is not the best way to approach this. Remember, unit tests are fast and cheap.

If you stumble upon the Presenter Pattern and find multiple approaches or different ways to describe it, you are not going crazy. There is little clear cut agreement on what a presenter is. What is common knowledge though is that it sits between the MVC layers. We use it to manage multiple model objects that need to be created at the same time. While combining these objects it imitates an ActiveRecord model. 

A word of caution. Do not overuse presenters—they should not be your first choice. Usually, the need for a presenter grows over time. For me personally, they are best used when you have data represented by multiple models that need to come together in a single view. Without a presenter, you might more often than not prepare multiple instance variables in your controller for a single view. That alone can make them real fat really quickly. A presenter combines them in a single object that can be served to the view via a single instance variable.

A thing that you should consider and weigh is that presenters add objects to your codebase. At the same time, it also reduces the number of objects a controller needs to deal with and reduces complexity by that. Essentially, such a presenter is a class that contains the entire state for a given view. It is probably a fairly advanced technique to lose some fat, but when you want to slim down, you need to put in the work. 

## Non-RESTful Controllers

Not trying to adhere to the standard controller actions is most likely a bad idea. Having tons of custom controller methods is an antipattern you can avoid pretty easily. Methods like ```login_user```, ```activate_admin```, ```show_books```, and other funny business that stands in for ```new```, ```create```, ```show``` and so forth, should give you a reason to pause and to doubt your approach. Not following this REST-ful approach can easily lead to big, massive controllers, mostly likely because you’ll need to fight the framework or reinvent the wheel every once in a while. In short, not a good idea. Also, more often than not, it’s also a symptom of inexperience or carelessnes. Following the “Single Responsibility Principle” seems to be very hard under these circumstances as well—just an educated guess though.

Approaching resources in your controller in a  restful manner is making your life a lot easier and your apps are becoming easier to maintain as well—which adds to the overall stability of your app. Think about handling resources REST-fully from the perspective of an object’s life cycle. You create, update, show (single or collections), update and destroy them. For most cases, this will do the job. FYI, ```new``` and ```edit``` actions aren’t really part of REST—they are more like different versions of the ```show ``` action, helping you present different stages in the resource’s life cycle. Put together, most of the time, these seven standard controller actions give you all you need to manage your resources in your controllers. Another big advantage is that other Rails developers working with your code will be able to navigate your controllers much faster.

Following that line of REST-ful cool aid, this also includes the way you name your controllers. The name of the resource you work on should be mirrored in the controller object. For example, having a ```MissionsController``` that handles other resources than ```@mission``` objects is a smell that something is off. The sheer size of a controller often is also a dead giveaway that REST was ignored. Should you encounter large controllers that implement tons of customized methods that break with conventions, it can be a very effective strategy to split them into multiple distinctive controllers that have focused responsibilities—and bascially manage only a single resource while adhering to a REST-ful style. Break them apart agressively and you will have an easier time to compose their methods the Rails way.

## Rat’s Nest Resources

Look at the following example and ask yourself what’s wrong with this:

#### Nested AgentsController

##### app/controllers/agents_controller.rb

``` ruby

class AgentsController < ApplicationController
  def index
    if params[:mission_id]
      @mission = Mission.find(params[:mission_id])
      @agents = @mission.agents
    else
      @agents = Agent.all
    end
  end
end

```

Here we check if we have a nested route that provides us with the id for a possible ```@mission``` object. If so, we want to use the associated object to get the ```agents``` from it. Otherwise, we’ll fetch a list of all agents for the view. Looks harmless, especially because it’s still concise, but it’s the start of a potentially way larger rat’s nest.

#### Nested Routes

``` ruby

resources :agents
resources :missions do
  resources :agents
end

```

Nothing obtuse about the nested routes here. In general, there is nothing wrong about this approach. The thing we should be careful about is how the controller handles this business—and as a consequence, how the view needs to adapt to it. Not exactly squeaky clean as can see below.

#### View With Unnecessary Conditional

##### app/views/agents/index.html.erb

``` erb

<% if @mission %>
  <h2>Mission</h2>
  <div><%= @mission.name %></div>
  <div><%= @mission.objective %></div>
  <div><%= @mission.enemy %></div>
<% end %>

<h2>Agents</h2>
<ul>
  <% @agents.each do |agent| %>
    <li class='agent'>
      <div>Name:            <%= agent.name %></div>
      <div>Number:          <%= agent.number %></div>
      <div>Licence to kill: <%= agent.licence_to_kill %></div>
      <div>Status:          <%= agent.status %></div>
    </li>
  <% end %>
</ul>

```

Might also not look like a big deal, I get it. The level of complexity is not exactly real world though. Aside from that, the argument is more about dealing with resources in an object oriented way and about using Rails to your fullest advantage. I guess this is a little bit of an edge case regarding single responsibilities. It’s not exactly violating this idea too bad, even though we have a second object—@mission—for the association lingering around. But since we are using it for getting access to a specific set of agents, this is totally alright.

The branching is the part that is inelegant and will most likely lead to poor design decisions—both in views and controllers. Creating two versions of ```@agents``` in the same method is the perpetrator here. I’ll make it short, this can get out of hand really quickly. Once you start nesting resources like this, chances are good new rats are hanging around soon. And the view above also needs a conditional that adapts to the situation for when you have ```@agents``` associated with a ```@mission```. As you can easily see, a little bit of sloppiness in your controller can lead to bloated views that have more code than needed. Let’s try another approach. Exterminator time! 

#### Separate Controllers

Instead of nesting these resources, we should be giving each version of this resource its own distinctive, focused controller—one controller for “simple”, unnested agents and one for agents that are associated with a mission. We can achieve this via namespacing one of them under a ```/missions``` folder. 

##### app/controllers/missions/agents_controller.rb

``` ruby

module Missions
  class AgentsController < ApplicationController
  
    def index
      @mission = Mission.find(params[:mission_id])
      @agents = @mission.agents
    end
  end
end

```

By wrapping this controller inside a module, we can avoid that ```AgentsController``` inherits twice from ```ApplicationController```. Without it, we would run into an error like this: ```Unable to autoload constant Missions::AgentsController```. I think a module is a small price to pay for making Rails happy with autoloading. The second ```AgentsController``` can stay in the same file as before. It now only deals with one possible resource in ```index```—prepping all agents without missions that are around. 

##### app/controllers/agents_controller.rb

``` ruby

class AgentsController < ApplicationController

  def index
    @agents = Agent.all
  end
end

```

Of course, we also need to instruct our routes to look for this new namespaced controller if agents are associated with a mission.

``` ruby

resources :agents
resources :missions do
  resources :agents, controller: 'missions/agents'
end

```

After we specified that our nested resource has a namespaced controller, we’re all set. When we do a ```rake routes``` check in the terminal, we’ll see that our new controller is namespaced and that we are good to go.

#### New Routes

``` bash

 Prefix Verb   URI Pattern                                     Controller#Action
              root GET    /                                               agents#index
            agents GET    /agents(.:format)                               agents#index
                   POST   /agents(.:format)                               agents#create
         new_agent GET    /agents/new(.:format)                           agents#new
        edit_agent GET    /agents/:id/edit(.:format)                      agents#edit
             agent GET    /agents/:id(.:format)                           agents#show
                   PATCH  /agents/:id(.:format)                           agents#update
                   PUT    /agents/:id(.:format)                           agents#update
                   DELETE /agents/:id(.:format)                           agents#destroy
    mission_agents GET    /missions/:mission_id/agents(.:format)          missions/agents#index
                   POST   /missions/:mission_id/agents(.:format)          missions/agents#create
 new_mission_agent GET    /missions/:mission_id/agents/new(.:format)      missions/agents#new
edit_mission_agent GET    /missions/:mission_id/agents/:id/edit(.:format) missions/agents#edit
     mission_agent GET    /missions/:mission_id/agents/:id(.:format)      missions/agents#show
                   PATCH  /missions/:mission_id/agents/:id(.:format)      missions/agents#update
                   PUT    /missions/:mission_id/agents/:id(.:format)      missions/agents#update
                   DELETE /missions/:mission_id/agents/:id(.:format)      missions/agents#destroy

```

Our nested resource for ```agents``` is now properly redirected to ```controllers/missions/agents_controller.rb``` and each action can take care of agents that are part of a mission.

## Final Thoughts

I think if you as a beginner can avoid these AntipPatterns in your controllers you are off to a very good start. There is still much left to learn for you in this regard but give it time, it’s nothing that comes too easy or overnight. On the other hand if you tasted blood and like to explore more advanced techniques I’m all for. Take your time, have fun and don’t get frustrated if you need to revisit the topic because you have not all pieces of the puzzle in place yet. If you are early in the development game and started to play with design patterns, I believe you are way ahead of the game and made the right decision no to wait and get out of your comfort zone to stretch your gray matter a bit.




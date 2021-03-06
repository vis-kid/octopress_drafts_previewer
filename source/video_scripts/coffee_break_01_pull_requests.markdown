In this short coffee break we are gonna look into GitHub pull requests. This one is especially made for those who have recently been learning a lot via tutorials, books, blog posts and the like and who are maybe interested in taking the next step by contributing to Open Source projects. This can be intimidating, espcially if you are new to the party and are unfamiliar with the pull requests workflow for contributing to projects. Let’s get this out of the way and show you where to start.

## Contributing to Projects aka Pull Requests

People also call the involved workflow, “Topic Branches workflow”. Why? Simple! Let’s have a look. Let’s have a quick look what we are talking about:

--------------


animation:
+ Clone or fork project
+ Create descriptive topic branch
+ Make your changes
+ Commit changes on that topic branch 
+ Push your new topic branch up to your forked remote repo
+ Your remote repo will have noticed these changes and will offer you to “compare and pull request” the changes. On GitHub you basically just follow the big green button.

--------------

## git clone && git fork that is the question

The question of cloning or forking comes down to: do you have push access or not? If you don’t have push access, you need to fork the project. With a fork, you own that specific copy of a project. If you make any changes, even to master, it does not affect the original codebase in any way. Only via a pull request will you be able to submit changes to that codebase—and only if it gets accepted by the maintainers of that codebase. It even will be under your username’s namespace—like yourusername/project-name. The cool thing about this fork is that you have push access right away.

If you have that push access you simply clone the project down to your machine. Cloned or forked, it shouldn’t change the workflow. Why? Because pull requests are an excellent way for getting a code review and feedback. The differeence is how you need to set up your project at the start. In both cases you are pulling down a project onto your machine for development. Both are copies of the project at its latest state of the remote repo.

For now, let’s look at a more typical example of how to contribute to Open Source projects via a fork.

You go to a public repo on GitHub and press the fork button

--------------------

Fork button

------------------

---------------------------

Animation: Fork and Clone
Sheep and Fork

---------------------------

After we forked a project under our own namespace and get push access that way, we clone the new forked repo down onto our machines to apply changes via our preferred code editor. With the project locally cloned, we just need to create a topic branch and apply our changes on it. After you are fnished with your work, you need to push your branch with your changes first before you create a pull request.

With a fork, it basically breaks down to changes that are suggested and merged between 2 repositories—the original one and your forked one. But this workflow does not only work effectively in that way. Two branches from the same repository can also follow the same workflow and reap the same benefits. So you can also open a pull request while having push access and working on the same repo with your team. The same workflow applies. You push your topic branch to the remote repo and open a pull request. This initiates the same code review, discussion and merge process. That way you don’t need to fork and work on a separate repo.

------------------

topic branch

-----------------

## Topic Branch

!! well named

Why not work directly on the master branch? Simple! You push your changes up to your forked repo via a focused topic branch that can then easily be merged in that way—if no merge conflicts arise of course. If you would do this work directly on the master branch and your suggested changes would be rejected, you’d end up with that work lying around on master and would need to rewind your work on that branch.

When you have pushed your changes up to the remote repo, the pull request is still not finished yet—not even initiated. What you need to do is activate this process via the pull request button in your repo. This is basically a notification to the maintainers of the original reop—a pull request.

The will output a summary of the changes you want to suggest. It gives other people an opportunity to chip in on a conversation about this particular change and is often a basis for a  discussion of additional changes—or an explanation why something might not be a good fit for a merge. No rocket science really, but maybe a bit intimidating at first. If you are asked to supply additional changes via new commits, you simply work some more on your already used topic branch and push these changes up to your remote branch again and thereby update the pull request automatically. Don’t forget to ping the maintainer that you updated the work!

----------------------------

animation:
+ use topic branch from master
+ Make additional commits
+ Push branch to remote repo
+ Update pull request
+ Discuss or continue work on branch
+ Maintainer merges in or closes branch

--------------------------------

If you have a good commit message you might not need to add additional information when you open the pull request. But you have the option to. Make good use of it if needed. Cryptic descriptions might not work in your favor though.

In the pull request, you and the maintainers of the project will be able to see the diffs this pull request would introduce. It’s a diff from the master branch in the original codebase and your suggested code changes in your topic branch.

What you should also be aware of is that this workflow makes the lives of project maintainers easier as well. Instead of giving potentially thousands of people push access to repos—and manage all that—a fork is self-contained by whoever is interested in that project and can give back to the original project by opening a pull request. A simple and elegant workflow to a complicated problem. GitHub, BitBucket and the likes all support this style of working—in fact, they count on it. The maintainers then check the list of pull requests and once a pull request was accepted—often after much discussion around it—the person with push access can merge the suggested changes into the original codebase.

----------------------------------

Animation: Merge Pull Request

--------------------------------

After the pull request has been merged, you are given the option to delete the branch you were working on. You local branch from that pull request will still be on your machine until you delete it as well. There is one question left. How do I update my fork with changes from the original repository?  

---------------------------


Animation: Clone & Fork & Upstream


-------------------------

That will result in your fork where you can make all the changes you desire and having access to the upstream from the original repo where you can fetch and pull changes to the orginal repo.


?? You have the original repo as an upstream remote from which you can fetch and pull. You simply pull down these changes in whatever fashion suits you best and you are up to date to make new changes.


In the end, pull request stay around. They are sort of a reference to these discussions and the credit goes to the person who did the pull request.

Merge PR or close PR


Remotes are ??




## Why Pull Requests

They are a core functionality you should learn in order to make changes to remote Git repositories. For beginners this seems a little daunting but they are actually not that complicated. They are part of a workflow that has been very effective for Open Source contributions. But any team can profit from this kind of workflow.

~~~ means of submitting a change to an existing codebase. It doesn’t matter if you are a core member of a team or if you forked the repository as a new contributor.

For a project, work on a change and submit your suggested code changes via a pull request. In that sense, a pull request is a proposal to the team or the maintainer of a codebase. 

GitHubs web based tools

---------------------------------

Animation: compare & pull request??

--------------------------------

In terms of etiquette, this has also the benefit that the you can suggest a correction instead of a mere complaint. Make an edit, improve the code and if the changes are beneficial the pull request gets merged—everybody wins.

Same workflow is beneficial if the code is not open sourced. The reason this workflow has become so popular is because it fosters collaboration effectively.

You work on a branch, do your work and send your pull request as a means to get a code review.

----------------------------

Animation: create branch with commits and pull request

--------------------------










## Continuous Integration Hooks

Tavis CI can intergrate with pull requests. They run their services after you submit a pull request and report back if your suggested changes are appropriate or effective for example. You will receive a build status that tell you if all your tests have passed successfully or if you would run into some sort of problem merging it into the codebase.

Predictive merge of a branch and the codebase.

So there is human feedback and CI status integrated into the idea of a pull request.





create branch, do some commits and when you are done send it back to the rest of your team via a pull request. The cool thing is that a pull request like that is a conversation—even if it won’t get merged in in the end. Tools like GitHub provide you with the tools to have useful conversations around these proposed code changes. The dialogue and discussion part of a pull request is more powerful than it might look at first. Often you will get some very useful suggestions for further changes and you can learn a lot from other developers along the way.

It can also be educational for newer people on the team. They can follow along the history of a project and see what is expected of suggested changes to the codebase. The culture of a team is often reflected along these lines as well.

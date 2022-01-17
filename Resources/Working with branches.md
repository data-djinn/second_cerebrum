[[git]][[Tech]]

# What is a branch?

If you don't use version control, a common workflow is to create different subdirectories to hold different versions of your project in different states, for example `development` and `final`. Of course, then you always end up with `final-updated` and `final-updated-revised` as well. The problem with this is that it becomes difficult to work out if you have the right version of each file in the right subdirectory, and you risk losing work.

One of the reasons Git is popular is its support for creating **branches**, which allows you to have multiple versions of your work, and lets you track each version systematically.

Each branch is like a parallel universe: changes you make in one branch do not affect other branches (until you **merge** them back together).

Note: Chapter 2 described the three-part data structure Git uses to record a repository's history: _blobs_ for files, _trees_ for the saved states of the repositories, and _commits_ to record the changes. Branches are the reason Git needs both trees and commits: a commit will have two parents when branches are being merged.

## See what branches your repo has
By default, every Git repository has a branch called `master` (which is why you have been seeing that word in Git's output in previous lessons). To list all of the branches in a repository, you can run the command `git branch`. The branch you are currently in will be shown with a `*` beside its name.

## View the differences between branches?

Branches and revisions are closely connected, and commands that work on the latter usually work on the former. For example, just as `git diff revision-1..revision-2` shows the difference between two versions of a repository, `git diff branch-1..branch-2` shows the difference between two branches.

## Switch from one branch to another

You previously used `git checkout` with a commit hash to switch the repository state to that hash. You can also use `git checkout` with the name of a branch to switch to that branch.

Two notes:

1.  When you run `git branch`, it puts a `*` beside the name of the branch you are currently in.
2.  Git will only let you do this if all of your changes have been committed. You can get around this, but it is outside the scope of this course.

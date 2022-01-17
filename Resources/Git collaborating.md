[[git]] [[Tech]]

## create brand new repository

If you want to create a repository for a new project in the current working directory, you can simply say `git init project-name`

One thing you should _not_ do is create one Git repository inside another. While Git does allow this, updating **nested repositories** becomes very complicated very quickly, since you need to tell Git which of the two `.git` directories the update is to be stored in. 

## clone existing repository
Sometimes you will join a project that is already running, inherit a project from someone else, or continue working on one of your own projects on a new machine. In each case, you will **clone** an existing repository instead of creating a new one. Cloning a repository does exactly what the name suggests: it creates a copy of an existing repository (including all of its history) in a new directory.

To clone a repository, use the command `git clone URL`, where `URL` identifies the repository you want to clone. This will normally be something like

```
https://github.com/datacamp/project.git
```

```
git clone /existing/project
```

will create a new directory called `project` inside your home directory. If you want to call the clone something else, add the directory name you want to the command:

```
git clone /existing/project newprojectname
```

## how to find where a cloned repository originated

When you a clone a repository, Git remembers where the original repository was. It does this by storing a **remote** in the new repository's configuration. A remote is like a browser bookmark with a name and a URL.

If you use an online git repository hosting service like GitHub or Bitbucket, a common task would be that you clone a repository from that site to work locally on your computer. Then the copy on the website is the remote.

If you are in a repository, you can list the names of its remotes using `git remote`.

If you want more information, you can use `git remote -v` (for "verbose"), which shows the remote's URLs. Note that "URLs" is plural: it's possible for a remote to have several URLs associated with it for different purposes, though in practice each remote is almost always paired with just one URL.

## How to define remotes
When you clone a repository, Git automatically creates a remote called `origin` that points to the original repository. You can add more remotes using:

```
git remote add remote-name URL
```

and remove existing ones using:

```
git remote rm remote-name
```

You can connect any two Git repositories this way, but in practice, you will almost always connect repositories that share some common ancestry.

## How to pull in changes from remote repo?

Git keeps track of remote repositories so that you can **pull** changes from those repositories and **push** changes to them.

Recall that the remote repository is often a repository in an online hosting service like GitHub. A typical workflow is that you pull in your collaborators' work from the remote repository so you have the latest version of everything, do some work yourself, then push your work back to the remote so that your collaborators have access to it.

Pulling changes is straightforward: the command `git pull remote branch` gets everything in `branch` in the remote repository identified by `remote` and merges it into the current branch of your local repository. For example, if you are in the `quarterly-report` branch of your local repository, the command:

## pulling when you have unsaved changes will cause problems
either commit local changes or revert, then pull again

## How can I push my changes to a remote repository?

The complement of `git pull` is `git push`, which pushes the changes you have made locally into a remote repository. The most common way to use it is:

```
git push remote-name branch-name
```

which pushes the contents of your branch `branch-name` into a branch with the same name in the remote repository associated with `remote-name`. It's possible to use different branch names at your end and the remote's end, but doing this quickly becomes confusing: it's almost always better to use the same names for branches across repositories.
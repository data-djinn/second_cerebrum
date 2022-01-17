[[git]][[Tech]][[Git basic workflow]][[Working with branches]][[Things all DevOps practictitioners do (WIP)]]


   

## View git config settings:

Like most complex pieces of software, Git allows you to change its default settings. To see what the settings are, you can use the command git config --list with one of three additional options:

-   --system: settings for every user on this computer.
-   --global: settings for every one of your projects.
-   --local: settings for one specific project.

Each level overrides the one above it, so local settings (per-project) take precedence over global settings (per-user), which in turn take precedence over system settings (for all users on the computer).

Most of Git's settings should be left as they are. However, there are two you should set on every computer you use: your name and your email address. These are recorded in the log every time you commit a change, and are often used to identify the authors of a project's content in order to give credit (or assign blame, depending on the circumstances).

To change a configuration value for all of your projects on a particular computer, run the command:

`git config --global setting value`

## Commit changes selectively

You don't have to put all of the changes you have made recently into the staging area at once. For example, suppose you are adding a feature to analysis.R and spot a bug in cleanup.R. After you have fixed it, you want to save your work. Since the changes to cleanup.R aren't directly related to the work you're doing in analysis.R, you should save your work in two separate commits.

The syntax for staging a single file is git add path/to/file.

If you make a mistake and accidentally stage a file you shouldn't have, you can unstage the additions with git reset HEAD and try again.

## Undo changes to unstaged files

How can I undo changes to unstaged files?

Suppose you have made changes to a file, then decide you want to undo them. Your text editor may be able to do this, but a more reliable way is to let Git do the work. The command:

`git checkout -- filename`

will discard the changes that have not yet been staged. (The double dash -- must be there to separate the git checkout command from the names of the file or files you want to recover.)

Use this command carefully: once you discard changes in this way, they are gone forever.

## Undo changes to staged files

At the start of this chapter you saw that git reset will unstage files that you previously staged using git add. By combining git reset with git checkout, you can undo changes to a file that you staged changes to. The syntax is as follows.
```
git reset HEAD path/to/file  
git checkout -- path/to/file
```
(You may be wondering why there are two commands for re-setting changes. The answer is that unstaging a file and undoing changes are both special cases of more powerful Git operations that you have not yet seen.)
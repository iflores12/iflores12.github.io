---
layout: post
title: "Understanding Git: Part One"
date: 2024-08-09 17:00:00 -0400
categories: git
---

I use git for work every day. While I have a certain workflow down and am able to use it for various tasks at work, I don't know the internals. I wanted to take a deep dive into git and understand the tool that is an integral part of my daily workflow. This is me reading the [pro git book](https://git-scm.com/book/en/v2) and taking notes so you don't have to. This is part one of the series.
# Intro

**Version Control:** Think of Google Sheets where you can rewind time. You can recall specific versions of the sheet and see who made edits. In this context though it's the set of files in your codebase
* Git is a distributed version control system. Where essentially each computer has an entire backup of the code. The clients have a full mirror of the repository as opposed to just the latest snapshot of files.

## What is Git

> Git thinks of its data more like a series of snapshots of a miniature filesystem. With Git, every time you commit, or save the state of your project, Git basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot. To be efficient, if files have not changed, Git doesn’t store the file again, just a link to the previous identical file it has already stored. Git thinks about its data more like a **stream of snapshots**.

Because it's distributed Git only needs local files to operate. You also don't need to talk to a server to figure out metadata on files. It grabs all of that from the local database. Everything in git is also checksummed which means it knows about every single change. Git knows all.

### Three stages
* modified: you've changed the file. but the database doesn't know about it yet
* staged: you've marked the file
* committed: the data is stored in the database

> The basic Git workflow goes something like this:
> 1. You modify files in your working tree.
> 2. You selectively stage just those changes you want to be part of your next commit, which adds _only_ those changes to the staging area.
> 3. You do a commit, which takes the files as they are in the staging area and stores that snapshot permanently in your Git directory.

# Getting Started

> You typically obtain a Git repository in one of two ways:
> 1. You can take a local directory that is currently not under version control, and turn it into a Git repository, or
> 2. You can _clone_ an existing Git repository from elsewhere.

Option #1
* Once you're inside the directory that you want to be in run `git init`
	* creates a subdirectory called .git that will contain all the git-related files
* If it's a new directory and there's no files. Go ahead and write some files
	* add those files with `git add .`
		* this adds all files from the directory you're in downwards
		* you can also add by doing `git add` and then the file name
	* run `git commit -m 'commit message'`
* If it's an existing directory you can do the same as above.

Option #2
* `git clone [URL]`
	* this will create a directory and initialize a .git directory
	* it will also pull down the data/files from the URL

## Basic Commands

Files in your repo will now be in one of two states:
1. tracked: they were in the last snapshot. Git knows about the file
2. untracked: Git does not know about the file.

Use the following command to check the status of the files in your repo: `git status`

Once you have new files or modified files you'll want to use the command `git add `

> ✏️ Note
>
> Git saves the state of your file as it was when you ran git add. So if you go back and modify the file after the fact you'll see that the file is staged and unstaged. If you were to commit the previous version of the file (the state when you first added it) would be the state that gets committed. Remember to add files again if you modify them so that they become staged.
>

Git tracks every new file and modification. But sometimes you don't want that because you'll have a build process that you don't want tracked. Or files with sensitive information that you don't want to be tracked. Or log files. To deal with those files you can create a file called `.gitignore`. Here you can add file patterns that you want git to ignore.
```
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf

# ignore all csvs
*.csv
```
Github actually has some pretty decent `.gitignore` files that you can use for your project.

To view all the unstaged changes you've made you can use the command `git diff`. This will not show all the changes since your last commit. It will only show unstaged changes. You'll probably want to use a different tool to do git diff. It can be a bit difficult if you're not used to it to do it from the terminal. Use the following command to see what tools are available to you `git difftool --tool-help`

If you want to skip the staging area you can do so by running `git commit -a`. This will both add your changes and commit them.


> ✏️ Note
>
> Unlike many other VCSs, Git doesn’t explicitly track file movement. If you rename a file in Git, no metadata is stored in Git that tells it you renamed the file. However, Git is pretty smart about figuring that out after the fact — we’ll deal with detecting file movement a bit later.

To actually move a file in git you'll have to run `git mv file new_file_location`


## Commit History

To view your commit history run `git log`
* You'll see output that has a commit, author, and date, with the commit message.
* By default, it will be ordered in reverse chronological order
	* most recent commits are first
* Use `-p` to show the diff of each commit
* Use `--stat` to print out how many files were changed within a commit as well as how many lines were added and removed
* Use `--pretty` to format the log output. There are a couple of prebuilt options that you can choose from. But you can also specify a format of your own choosing.
* You can add a `-[NUM]` to the end of git log to limit how many commits you want to see
* `git log -S [FUNCTION NAME]` will string match and show only those commits that changed the number of occurrences of that string.
* `git log -- path/to/file` will show you just the commit history of that file

## Undoing Things

### Amending

At some point while writing code and modifying files you will mess up and commit code that you did not want to commit. You have to be extra careful here because not everything can be undone and sometimes you can even corrupt your files and commit history if you're not paying attention.

If you commit too early and you forget to add some files, mess up the commit message, want to redo the commit or make additional changes then you'll use the command `git commit --amend`

This will take your staging area and use it for the commit. If nothing has changed then the only thing that happens is that the commit message changes.

### Unstaging

If you accidentally added files to the staging area after doing a blanket `git add *` and you want to remove the file from it you can run the command `git restore --staged <file>`. Git will actually show you this command when you do `git status` if you ever forget.

If you want to unmodify a modified file because you realize that the changes you made don't make sense then you can easily unmodify it. You can revert it back to what it looked like in it's last commit. To do so run `git restore <file>`. This will work if you have not staged the file. If you have staged then first restore it from the staging area and then restore the file again. This will revert all your changes.

> ⚠️ WARNING:
>
> It’s important to understand that `git checkout -- <file>` is a dangerous command. Any local changes you made to that file are gone — Git just replaced that file with the last staged or committed version. Don’t ever use this command unless you absolutely know that you don’t want those unsaved local changes.

### Remote

When you work collaboratively with others you'll need to manage a remote repo. These are repos that are hosted on the network somewhere or on a website like Github. This means that you'll have to be constantly pulling data and pushing data to the remote repos. You'll have to manage remotes and branches that no longer exist or new branches that you make locally.

To view what remote servers you have configured just run the command `git remote`. This will list all the remote servers you have. For the most part, you should just see `origin`. If you add the `-v` flag it will show you the url.

To get data from a remote repo you can use the command `get fetch`. This will pull down all the data from the remote project that you don't have in your local repo. You can also use `git pull` to both fetch new data from the remote repo and merge that remote into your current branch.

To push data the command is `git push origin [BRANCH]`.

### Git Aliases

```console
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

Here are some common aliases that you might want to set up. These are related to cli aliases if you're familiar with them. If not it basically means a shortcut that represents a longer/complex command. So using the above aliases instead of saying `git commit` you just have to type out `git ci`.

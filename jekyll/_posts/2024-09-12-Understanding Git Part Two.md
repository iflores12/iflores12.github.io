---
layout: post
title: "Understanding Git: Part Two"
date: 2024-09-12
categories: git
---

Welcome back to part two of the series. The first part covered a short history of git and some basic commands. Part two will deal with branching and distributed workflows

# Branches

You create branches when you need to diverge from the main line of development. You want to add a new feature or fix something without disrupting the main line so you create a branch to work off of. 

Because git stores differences as snapshots, when you commit, that object has a pointer to the snapshot. There are also pointers to objects that came before it (zero parents for the initial commit, one parent for a normal commit, and multiple parents for a commit that results from a merge of two or more branches). So a branch is just a pointer to a commit 

## New Branch

When you create a new branch you create a new pointer that can be moved around. How would you make a new branch?
```
git branch [BRANCH NAME]
```

At work rather than creating a branch in this fashion and then checking it out I like to combine the two commands by doing the following:
```
git checkout -b [BRANCH NAME]
```

### HEAD

Git has a special pointer called HEAD and this keeps track of what branch you're currently on. 

## Switching Branches

If you need to go between branches you can use the `git chekcout` command. For example, if you're working in branch-a and need to switch to branch-b:
```
git checkout branch-b
```


> [!NOTE] Note
> It’s important to note that when you switch branches in Git, files in your working directory will change. If you switch to an older branch, your working directory will be reverted to look like it did the last time you committed to that branch. If Git cannot do it cleanly, it will not let you switch at all.


# Basic Branching and Merging

Simple Workflow:
1. Do some work on a website.
2. Create a branch for a new user story you’re working on.
3. Do some work in that branch.

If something comes up then:
1. Switch to your production branch.
	* Git won't let you switch to another branch if you have uncommitted work in the branch you were previously working on. So if you were working on a branch for a new user story and didn't commit that work git would stop you from switching to `main`
		* This is due to a conflict between the current branch and the branch you're checking out
		* You could `stash` if you didn't want to commit
1. Create a branch to add the hotfix.
2. After it’s tested, merge the hotfix branch, and push to production.
3. Switch back to your original user story and continue working.


> [!NOTE] Note
> When you try to merge one commit with a commit that can be reached by following the first commit’s history, Git simplifies things by moving the pointer forward because there is no divergent work to merge together — this is called a “fast-forward.”

When you deploy the hotfix, before you go back to the user-story that you were working on you'll want to delete the hotfix branch. 
```
git branch -d hotfix
```

After switching branches you'll want to run `git pull origin main` if you need the changes in the branch you were originally working on. 

## Merge Conflicts

There will be times when merging will go awry and if you changed some part of a file that your coworker also changed in another branch you will run into merge conflicts. You'll see:
```console
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
Git has paused your merge process and you'll have to resolve the conflict. It will be easy to tell if you run `git status`. Git will show you which files have conflicts and when you open them up you'll see the following that will tell you this is the part of the file that has conflicts:
```html
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

I would recommend getting a visual merge tool to deal with conflicts. It will be a lot easier than doing them using `git mergetool`. For example vscode has pretty decent built in tooling for this sort of thing. If you're using Github you can also resolve merge conflicts from the web ui.

## Branch Management

Some more advanced commands:
* `git branch -v` will show you the last commit in each branch
* `git branch --merged` will show you branches that you've merged into the branch that you're currently in. Similarly with `git branch --no-merged`
* `git branch -d` will delete the branch
* `git branch l` will list out local branches

### Workflows

#### Long-Running Branches

You can leave branches open for a very long time. Git has no issue with this, and because of that some developers leave various branches open depending on the stage of development. You'll have the most stable code in `main` and then a developer will have a parallel branch named `develop`. This is where they test stability and when it finally does become stable they merge it into `main`.

#### Topic Branches

This is a short lived branch that you create for a feature or specific set of work. 

### Rebasing

You have two options to pull in changes from one branch into another `merge` and `rebase`. With `rebase` you can take all of the commands that were committed in one branch and replay them on a different branch. This works by going back to the common ancestor of the branches then it will get the diff commit by commit, it saves these diffs in temp files, resetting the current branch to the same as the branch you are rebasing onto, and applying each change. 

While you might not end with a different end product. Rebasing does produce a cleaner history because it will look like a linear history. 

> [!NOTE] Watch Out
> **Do not rebase commits that exist outside your repository and that people may have based work on.** When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different. If you push commits somewhere and others pull them down and base work on them, and then you rewrite those commits with `git rebase` and push them up again, your collaborators will have to re-merge their work and things will get messy when you try to pull their work back into yours.


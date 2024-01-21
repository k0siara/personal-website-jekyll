---
title: 'Best Git Practices For Managing Your Project'
date: 2021-04-29
permalink: /blog/best-git-practices-for-managing-your-project/
tags:
  - cool posts
  - category1
  - category2
---

## Having troubles managing git history in your project? You should read this.

In this article we’ll talk about best git practices for managing your project. Follow these rules to keep your project well-documented and to avoid future problems with your project’s git tree.

## One last thing before we start

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

## Creating new branches

### Main branches

At the core, the development model is greatly inspired by existing models out there. The central repository holds two main branches with an infinite lifetime:

- master branch
- develop branch

### Support branches

Next to the main branches master and develop, our development model uses a variety of supporting branches to aid parallel development between team members, ease tracking of features, prepare for production releases and to assist in quickly fixing live production problems. Unlike the main branches, these branches always have a limited lifetime, since they will be removed eventually.

The different types of branches you may want to use are:

- Feature branches (for ex. **feature/<TASK_ID>-Short-feature-description**)
- Defect branches (for ex. **defect/<TASK_ID>-Short-defect-description**)
- Release branches (for ex. **release/<release_version>**)
- Hotfix branches (for ex. **hotfix/<short_hotfix_description>**)

## Committing

### Use proper naming!

Generally, there are a few approaches for commit naming. You should choose one of them and stick to it. Remember to tell exactly what you did in a commit. Bad naming can cause difficulties in finding changes/bugs in your git tree.

For example, naming like this **IS UNNACEPTABLE**:

- “added ui”
- “added logic”
- “resolved PR comments part 2”
- “rebasing”
- etc.

Trust me, I’ve seen things like this in a few projects and the git tree was a mess…

### Preferably, use one of these approaches:

1.Imperative mood
[If applied, this commit will] **Remove deprecated methods from extensions**

2. Past tense
[By applying this commit, I] **Removed deprecated methods from extensions**

3. What does the commit do?
[This commit] **Removes deprecated methods from extensions**

---

**REMEMBER TO ALWAYS ADD TASK ID, IF YOU HAVE ONE:**

So finally, the commit would look like this:
**“ABC-123 Removes deprecated methods from extensions“**

---

## Amending commits

If you forgot about something (for example reformatting, removing whitespaces, etc.) in the last commit or you want to change it’s name DO NOT create a new commit if it is not necessary. Just amend you last commit, by using:

    git commit --amend

Once you do that, a new commit is created and we will push it ON TOP of the old commit. That’s why we need to use push force:

    git push -f origin feature/ABC-123_Some_cool_feature

## Merging VS Rebasing

The first thing to understand about `**git rebase**` is that it solves the same problem as `**git merge**`. Both of these commands are designed to integrate changes from one branch into another branch—they just do it in very different ways.

Consider what happens when you start working on a new feature in a dedicated branch, then another team member updates the `develop` branch with new commits. This results in a forked history, which should be familiar to anyone who has used Git as a collaboration tool.
![](__GHOST_URL__/content/images/max/800/1-YgZSJJ70NAl97pwBTJhGiw.png)
Now, let’s say that the new commits in `develop` are relevant to the feature that you’re working on. To incorporate the new commits into your `feature` branch, you have two options: merging or rebasing.

### Merging

The easiest option is to merge the `develop` branch into the feature branch. This creates a new “merge commit” in the `feature` branch that ties together the histories of both branches, giving you a branch structure that looks like this:
![](__GHOST_URL__/content/images/max/800/1-eJK_Uufb6gataxrqWkolKw.png)
Merging is nice because it’s a *non-destructive* operation. The existing branches are not changed in any way. This avoids all of the potential pitfalls of rebasing (discussed below).

On the other hand, this also means that the `feature` branch will have an extraneous merge commit every time you need to incorporate upstream changes. If `master` is very active, this can pollute your feature branch’s history quite a bit. While it’s possible to mitigate this issue with advanced `git log` options, it can make it hard for other developers to understand the history of the project.

On the other hand, this also means that the `feature` branch will have an **extraneous merge commit every time you need to incorporate upstream changes**. If `develop` is very active, this can pollute your feature branch’s history quite a bit. While it’s possible to mitigate this issue with advanced `git log` options, **it can make it hard for other developers to understand the history of the project**.

### Rebasing

This moves the entire `feature` branch to begin on the tip of the `**develop**`branch, effectively incorporating all of the new commits in `**develop**`. But, instead of using a merge commit, rebasing *re-writes* the project history by creating brand new commits for each commit in the original branch.
![](__GHOST_URL__/content/images/max/800/1-VZzjzmTrEsfXyjw7SPZCmA.png)
The major benefit of rebasing is that you get a much cleaner project history. First, it eliminates the unnecessary merge commits required by `git merge`. Second, as you can see in the above diagram, rebasing also results in a perfectly linear project history — you can follow the tip of `feature` all the way to the beginning of the project without any forks. This makes it easier to navigate your project with commands like `**git log**`, `**git bisect**`, and `**gitk**`.

But, there are two trade-offs for this pristine commit history: safety and traceability. If you don’t follow the [Golden Rule of Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) , re-writing project history can be potentially catastrophic for your collaboration workflow. And, less importantly, rebasing loses the context provided by a merge commit — you can’t see when upstream changes were incorporated into the feature.

Remember that after rebasing if commits are added to our past then you have to PUSH FORCE them to your branch. If commits are added on the top (for example rebasing feature branch to develop, then you can use a simple push).

## The Verdict

Use rebase if you want to get the latest changes from develop or other branch. It does not create a mess in git tree like merge, so the project is easier to read/learn and old unwanted changes are easier to edit too.

---

**BUT**

If you’re not the only one working on a branch and for example someone changed the history/amended commit/squashed them/etc. it’s better to use merge. If you’d want to use rebase in this situation you’d have to reset your branch and cherry pick the changes from you other brach for example.

---

## Useful git commands that you MUST learn to save yourself in critical situations

### Git stash

The `**git stash**` temporarily shelves (or stashes) changes you’ve made to your working copy so you can work on something else, and then come back and re-apply them later on. Stashing is handy if you need to quickly switch context and work on something else, but you’re mid-way through a code change and aren’t quite ready to commit.

### Git status

The `**git status**` command displays the state of the working directory and the staging area. It lets you see which changes have been staged, which haven’t, and which files aren’t being tracked by Git.

### Git log

The `**git log**` command displays committed snapshots. It lets you list the project history, filter it, and search for specific changes. While `**git status**` lets you inspect the working directory and the staging area, `**git log**` only operates on the committed history.

### Git reflog

Reflogs track when Git refs were updated in the local repository. In addition to branch tip reflogs, a special reflog is maintained for the Git stash. Reflogs are stored in directories under the local repository’s `**.git**` directory. `**git reflog**` directories can be found at `**.git/logs/refs/heads/.**`, `**.git/logs/HEAD**`, and also `**.git/logs/refs/stash**` if the `**git stash**` has been used on the repo.

### Git reset (soft/mixed/hard)

The `**git reset**` command is a complex and versatile tool for undoing changes. It has three primary forms of invocation. These forms correspond to command line arguments `**--soft**, **--mixed**, **--hard**`. The three arguments each correspond to Git’s three internal state management mechanism’s, The Commit Tree (`**HEAD**`), The Staging Index, and The Working Directory.

### Git rebase interactive

To modify a commit that is farther back in your history, you must move to more complex tools. Git doesn’t have a modify-history tool, but you can use the rebase tool to rebase a series of commits onto the HEAD they were originally based on instead of moving them to another one. With the interactive rebase tool, you can then stop after each commit you want to modify and change the message, add files, or do whatever you wish. You can run rebase interactively by adding the `-i` option to `git rebase`. You must indicate how far back you want to rewrite commits by telling the command which commit to rebase onto.

Running this command gives you a list of commits in your text editor that looks something like this:

    pick f7f3f6d Change my name a bit
    pick 310154e Update README formatting and add blame
    pick a5f4a0d Add cat-file
    # Rebase 710f0f8..a5f4a0d onto 710f0f8
    #
    # Commands:
    # p, pick <commit> = use commit
    # r, reword <commit> = use commit, but edit the commit message
    # e, edit <commit> = use commit, but stop for amending
    # s, squash <commit> = use commit, but meld into previous commit
    # f, fixup <commit> = like "squash", but discard this commit's log message
    # x, exec <command> = run command (the rest of the line) using shell
    # b, break = stop here (continue rebase later with 'git rebase --continue')
    # d, drop <commit> = remove commit
    # l, label <label> = label current HEAD with a name
    # t, reset <label> = reset HEAD to a label
    # m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
    # .       create a merge commit using the original merge commit's
    # .       message (or the oneline, if no original merge commit was
    # .       specified). Use -c <commit> to reword the commit message.
    #
    # These lines can be re-ordered; they are executed from top to bottom.
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    #
    # However, if you remove everything, the rebase will be aborted.
    #
    # Note that empty commits are commented out

### Git cherry pick

`**git cherry-pick**` is a powerful command that enables arbitrary Git commits to be picked by reference and appended to the current working **HEAD**. Cherry picking is the act of picking a commit from a branch and applying it to another. `**git cherry-pick**` can be useful for undoing changes. For example, say a commit is accidentally made to the wrong branch. You can switch to the correct branch and cherry-pick the commit to where it should belong.

## That’s a lot of new git commands!

I know that for for some of you these commands can be hard to understand. That’s why I encourage you to go, create a simple repository on Github, GitLab, Bitbucket or whatever you like. And then just.. **PLAY WITH IT**. Test every command and every option it has. You can either use the command line or software like Github Desktop, Sourcetree or GitKraken. Just learn how these commands work and when is the good time to use them. It will be very helpful for you, I assure you.

If you have any questions about Git in general, don’t be scared and reach out to me, cheers!
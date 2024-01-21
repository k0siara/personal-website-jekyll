---
title: 'Hacking The History — Git Amend'
date: 2021-04-23
permalink: /blog/hacking-the-history-git-amend/
tags:
  - cool posts
  - category1
  - category2
---

## Forgot to do something in your last commit? Take it easy, git amend to the rescue

Today you’re gonna learn one of many ways to hack and rewrite your history in git. Git amend is great and I use it literally everyday. It is perfect for situations when you already created a commit, but forgot about some changes or just simply want to edit the commit message.

💡

Remember!

Avoid messing with pushed history on branches, where you’re not the only one making changes. Shared branches are usually harder to maintain and you can easily mess up your teammate’s work.
If you’re an advanced git user then you can do whatever you want

## One last thing before we start

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

## Here’s how it’s done:

First, we have to check our existing commit history, to do that we use **git log**. This is a record of all commits that are in your repo:

    git log

You should see something like this as the output:

    commit 355e47d4d495a7d10ec0814cd1e472b03ee77d1b (HEAD -> master, origin/master)Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:35:56 2021 +0200

        Improved styling

    commit 27d1bb599ce8f914c5e4d42b0ec08095a73c2f10Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:28:28 2021 +0200

        Created first file

As you can see I have a simple branch with two commits on it. Both of them have been pushed from my local to the remote. Unfortunately I forgot to add one more thing to **355e47d…** commit. What options do I have now?

Well… I can simply create a new commit with these changes and I’ll be fine:

    commit a3ee725e0a3e6265df4747cd7fc558e200b56dbe (HEAD -> master, origin/master)Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:40:24 2021 +0200

        Fixed typo

    commit 355e47d4d495a7d10ec0814cd1e472b03ee77d1bAuthor: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:35:56 2021 +0200

        Improved styling

    commit 27d1bb599ce8f914c5e4d42b0ec08095a73c2f10Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:28:28 2021 +0200

        Created first file

That was simple, it’s just a new commit. Isn’t this ok? Well, no. Commits are great and you should be doing small changes to the code and committing them with a good message, but creating a new commit to fix a typo, or to add one small thing you forgot about in the previous commit is just lazy and not good-looking.

How to do it properly

First we add some changes to the changelist:

    git add .

Then we amend it:

    git commit --amend

After executing this command, you should get something like this. This file will show up in your console or on your screen (most probably on vim/nano):

    Improved styling and added readme.mdPlease enter the commit message for your changes. Lines startingwith '#' will be ignored, and an empty message aborts the commit.#Date:      Mon Apr 26 21:35:56 2021 +0200#On branch masterChanges to be committed:modified:   a.txtnew file:   second.txt#

You can edit commit messages here and anything you’d like. After you finish editing just save and exit the file.

On vim you just use this to save and exit:

    :wq

Let’s see what **git reflog** says now. It is a record of all commits that are or were referenced in your repo at any time:

    4dad158 (HEAD -> master) HEAD@{0}: commit (amend): Improved styling...a3ee725 HEAD@{7}: commit: Fixed typo355e47d (origin/master) HEAD@{8}: commit: Improved styling27d1bb5 HEAD@{9}: commit (initial): Created first file

As you can see the new commit is created. But why did git create a new commit instead of overriding the old one? Well, git has to be able to come back to any branch state at any time, so instead the new commit is created and it will be pushed on top of the old one.

How to push the “new” commit?

Now to push the updated commit, we just use **push force**:

    git push -f origin master

Let’s check what commits do we see on our branch now:

    commit 4dad158f6574c96f6647ba1ce1fa2ce965884ce3 (HEAD -> master, origin/master)Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:35:56 2021 +0200

        Improved styling

    commit 27d1bb599ce8f914c5e4d42b0ec08095a73c2f10Author: pmarciszekkosieradzki patryk.kosieradzki@gmail.comDate:   Mon Apr 26 21:28:28 2021 +0200

        Created first file

That’s all folks. You now know how to amend commits. Hope you learned something and should you have any questions please do not hesitate to contact me
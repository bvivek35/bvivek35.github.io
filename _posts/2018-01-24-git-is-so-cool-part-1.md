---
layout: post
title:  "Git is so Cool Part-1"
date:   2018-01-24 22:10:00  -0800
categories: technical git
author: Vivek Bharadwaj
---

Git is awesome. Let's get that out of the way. I will be sharing some commands and options that you don't find on most tutorial sites, but is super useful in real world scenarios.

##### Stage only patches of a file

Imagine a scenario where you have made changes to a file and want to add certain changes (say changeset1) to commit1 and another set of changes (changeset2) into commit2.
Let's look at this example:

```python

def func1(): # changeset1
    pass

def origFunc(): # Exisiting file
    pass

def func2(): # changeset2
    pass
```

A typical solution could be:
1. Delete changeset2, maybe copy it to a temp file or even clipboard.
2. Commit changeset1.
3. Reapply changeset2 and commit.

Let's face it. This sucks. What if you can't just copy changeset2 to a temp file.
What if the changesets are intertwined more subtly.

```python
def func1(): # changeset2
    sendMail()
    pass 

def origFunc():
    changeStatus() # changeset1
    #Existing logic
    sendMail()

def sendMail(): # changeset2
    pass

```

In this case, changeset1 should contain `changeStatus()` only. Trying to delete all changeset2 entries is simply error prone and not good.
Using the `-p` switch on `git add` like `git add -p` will prompt git to consider every hunk of the file that has changed and will ask you to either add or ignore this hunk of the file for staging.
Look at the below:
* Pressed 's' to ask git to consider smaller hunks.
* 'y' instructs git to add this hunk and 'n' not to.

```plain
git add -p
```
![git-add-p]({{"/assets/posts/2018-01-git-is-so-cool/git-add-p.jpeg" | absolute_url}}){:height="50px"}

To show you changeset2 is yet to be staged (still marked red in git status), here's the output of git diff after staging changeset1:
```plain
git diff ex.py
```
![git-add-p]({{"/assets/posts/2018-01-git-is-so-cool/git-diff-after-staging-1.jpeg" | absolute_url}}){:height="50px"}

Hope this post eases your workflow. I will be sharing more interesting options that git has in the next few posts here on Coding Memoirs.
Keep an eye out!

Au Revoir

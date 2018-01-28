---
layout: post
title:  "Git is so Cool Part-2"
date:   2018-01-26 22:10:00  -0800
categories: technical git
author: Vivek Bharadwaj
---

If you have not already, check out the [first part of this series][part-1-link], where I talk about adding files to the staging area.

In this post, I will talk about `diff`, the command that literally tells us the difference between two sets of files.

##### Good ol' diff
In the context of git, the `--diff` option diffs the staged file (in the index) with the current file on the filesystem (clean or dirty). The point of this post isn't to harp over how to diff between index, file system, commits or branches or what not, but to use it more effectively when a diff is not straightforward.

Very often we come across sentences with such minute difference between them, it's hard to see what's changed. 
Take for example,
```plain
A quick brown fox jupms over the lazy dog.
A quick brown fox jumps over the lazy dog.
```

Observe that the difference between the two lines is just the spelling of `jumps`. This can be tricky to find especially when you are running a line-based diff command like `git diff`.
Here's what `git-diff` returns:
```plain
git --diff ex.txt
```
![git-diff]({{"/assets/posts/2018-01-git-is-so-cool/git-diff.jpeg" | absolute_url}}){:height="50px"}

Hardly useful given the text. It would take a trained eye to figure out the difference between the lines.
There has to be a better way.

##### Finer diff

The problem here is that diff is taking a line as a unit to run diff. What if we could instruct diff to take a finer unit. A `word` seems a good option. After all it is easier to sift through a few letters of a given word compared to a few words of a potentially large line. This is where `--word-diff` option comes into the scene. Passed alongside the `--diff` option to `git`, it runs diff with a word as it's unit.

Here's what it looks like:
```plain
git --diff --word-diff ex.txt
```
![git-diff]({{"/assets/posts/2018-01-git-is-so-cool/git-diff-word-diff.jpeg" | absolute_url}}){:height="50px"}

##### Much Finer diff

This is already much better, but what if we want to provide our own definition of a `word`, the unit of diff. In Computer Science, very few techniques let us succinctly express pattern like `regex`. It's only natural that our target unit of diff be passed along as a regex. Indeed, git has a `--word-diff-regex=<regex>` option that can be passed along side `--diff`.

Lets look at the finest grain of seperation, a character. In `regex` lingo, this is simply the regex `.`.

Putting it all together:
```plain
git --diff --word-diff-regex='.' ex.txt
```
![git-diff]({{"/assets/posts/2018-01-git-is-so-cool/git-diff-word-diff-regex.jpeg" | absolute_url}}){:height="50px"}

Hope this post eases your workflow. I will be sharing more interesting (hopefully) stories and posts here on Coding Memoirs.
Keep an eye out!

Au Revoir

[part-1-link]: https://bvivek35.github.io/technical/git/2018/01/24/git-is-so-cool-part-1/
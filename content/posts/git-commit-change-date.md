+++
date = "2016-11-15T19:29:07Z"
draft = false
title = "Changing the Date of Git Commits"
hideToc = true

+++

Have you ever looked at peoples Github contribution timelines and seen a word or some ASCII art. This is done by creating a repo, then manipulating the dates of commits, to create the desired design.

This blog post is not about creating funky pieces of art in your Github commit history timeline. But instead, shows you how to change the date of a commit using Git.

When you make a git commit you can actually set the date of the commit to anything you want. Lets get started.

First, you have to set two environment variables - _GIT_AUTHOR_DATE_ and _GIT_COMMITTER_DATE_, like so:

```
$ export GIT_AUTHOR_DATE="Wed Jan 10 14:00 2099 +0100"
$ export GIT_COMMITTER_DATE="Wed Jan 10 14:00 2099 +0100"
```

Create a git repo:

```
$ mkdir test
$ cd test
$ git init
$ echo "hello world" >> hello.txt
$ git add hello.txt
```

Now lets commit our changes:

```
$ git commit -m "initial commit"
$ git log
commit 80b2d1f499d6ae455a4f87f68e107b275abfefe8
Author: Philip J. Fry <fry@planet-express.com>
Date:   Wed Jan 10 14:00:00 2099 +0100

    initial commit
```

You can see the date of our commit has changed, rather the being the current date/time, it is set to day in the year 2099.

If you want to change the date of a commit in the past, I recommend reading this [blog post](http://eddmann.com/posts/changing-the-timestamp-of-a-previous-git-commit/).

Useful links:

[Git commit date formats](https://git-scm.com/docs/git-commit#_date_formats)

[Changing the timestamp of a previous Git commit](http://eddmann.com/posts/changing-the-timestamp-of-a-previous-git-commit/)

[gitfiti - abusing github commit history for the lulz](https://github.com/gelstudios/gitfiti)

Fin.

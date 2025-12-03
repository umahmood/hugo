+++
date = "2016-11-29T00:43:13Z"
draft = false
title = "Creating Empty Git Commits"
hideToc = true

+++

You may not know it, but Git allows you to create an empty commit in to your repo. It doesn't like it, but you can do it any way.

An empty commit is one where you don't actually commit any code changes i.e. if you _git status_ your repo, you get the message:

```
On branch master
nothing to commit, working tree clean
```

Why would you want to create an empty commit? You may want to communicate changes, which have nothing to do with code. But you have updated something that you want the rest of the team to know about. And you feel that communicating this via the git log make sense.

#### Creating an empty commit

Start by creating a repo:

```
$ mkdir test
$ cd test
$ git init
$ echo "hello world" >> hello.txt
$ git add hello.txt
$ git commit -m "initial commit"
```

Now we have a repo with one commit and nothing else to commit:

```
$ git status
On branch master
nothing to commit, working tree clean
```

We can now use the _--allow-empty_ flag to insert an empty commit:

```
$ git commit -m "this is an empty commit" --allow-empty
[master 02cdd7f] this is an empty commit
```

Lets see what the git log looks like:

```
$ git log
commit 02cdd7f74c0fe4219a061e94066251dbf1137475
Author: Philip J. Fry <fry@planet-express.com>
Date:   Thu Dec 01 21:24:28 2016 +0000

    this is an empty commit

commit f503c2ccf476b144f69108a842782464dd3ca61e
Author: Philip J. Fry <fry@planet-express.com>
Date:   Thu Dec 01 21:15:40 2016 +0000

    initial commit
```

We can also create an empty commit with an empty commit message. Using the _--allow-empty-message_ flag:

```
$ git commit --allow-empty-message --allow-empty
[master 8546295]

$ git log
commit 85462959fedf63fd4a59c578caccef4df8cceff8
Author: Philip J. Fry <fry@planet-express.com>
Date:   Thu Dec 01 21:57:47 2016 +0000

commit 02cdd7f74c0fe4219a061e94066251dbf1137475
Author: Philip J. Fry <fry@planet-express.com>
Date:   Thu Dec 01 21:24:28 2016 +0000

    this is an empty commit

commit f503c2ccf476b144f69108a842782464dd3ca61e
Author: Philip J. Fry <fry@planet-express.com>
Date:   Thu Dec 01 21:15:40 2016 +0000

    initial commit
```

Thats it! if you want to read more about the flags discussed go [here](https://git-scm.com/docs/git-commit#git-commit---allow-empty).

Fin.

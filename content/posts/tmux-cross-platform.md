+++
date = "2015-10-20T17:15:08+01:00"
draft = false
title = "tmux Cross Platform Config."
hideToc = true

+++

If like me you use tmux on both Linux and OS X, then managing your tmux configuration can be a pain. The problem is there is configuration that is specific to either OS, such as copy and paste behavior. This blog post will show you how to manage your tmux configuration across platforms in a better way.

The first thing you want to do is create a dot file for each platform, so for Linux create _.tmux-linux.conf_ and for OS X create _.tmux-osx.conf_. Create and place these files in the same location as your _.tmux.conf_ file (most likely your home directory).

Now move any OS specific settings out of _.tmux.conf_ into _.tmux-osx.conf_ and _.tmux-linux.conf_ respectively.

In your _.tmux.conf_ file add the line:

```bash
if-shell "uname | grep -q Darwin" 'source-file ~/.tmux-osx.conf' \
'source-file ~/.tmux-linux.conf'
```

Basically, when tmux reads in it's configuration, if the OS is 'Darwin' (OS X) then it will read _.tmux-osx.conf_ else it will read in _.tmux-linux.conf_.

Note: the above code snippet assumes that the three \*.conf files are placed in your home directory, if they are not change the paths.

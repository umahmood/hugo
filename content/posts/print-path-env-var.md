+++
title = "Make 'echo %PATH' More Readable"
date = "2017-11-18T14:34:22+01:00"
hideToc = true
draft = false
+++

When you want to check the contents of your _$PATH_ variable. To see which directories are in your session, you run the command:

```
$ echo $PATH
```

Output:

```
/usr/local/opt/coreutils/libexec/gnubin:/usr/local/bin:/usr/local/sbin:/opt:/usr/local/go/bin:/usr/local/texlive/2015basic/bin/x86_64-darwin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/opt/coreutils/libexec/gnubin:/usr/local/sbin:/opt:/usr/local/go/bin:/usr/local/texlive/2015basic/bin/x86_64-darwin
```

This is all well and good, but the output of the command can be hard to read if you have many directories in your path. What you want is a command that outputs the _$PATH_ variable in an easier to read format. Luckily, this can accomplished with a little bit of bash code:

```
path()
{
    p=$PATH;
    OIFS="$IFS";
    IFS=':';
    read -a paths <<< "${p}";
    IFS="$OIFS";
    for i in "${paths[@]}";
    do
        echo "- $i";
    done
}
```

Now if we run:

```
$ path
```

Output:

```
- /usr/local/opt/coreutils/libexec/gnubin
- /usr/local/bin
- /usr/local/sbin
- /usr/local/go/bin
- /usr/local/texlive/2015basic/bin/x86_64-darwin
- /usr/local/bin
- /usr/bin
- /bin
- /usr/sbin
- /sbin
- /usr/local/opt/coreutils/libexec/gnubin
- /usr/local/sbin
- /opt
- /usr/local/go/bin
- /usr/local/texlive/2015basic/bin/x86_64-darwin
```

This is much easier to read. And better, it still displays the original search order of the directories.

Fin.

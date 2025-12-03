+++
title = "Tar Archiving Using Relative Paths"
date = 2018-03-10T15:40:39+01:00
draft = false
hideToc = true
+++

If you want to archive a directory (using the tar command), but not include the absolute path to the directory. You can use the **-C** option to the tar command, which essentially cd's into the directory and then archives it. Here is an example:

```
$ tar czf /tmp/my_backup.tar.gz -C ~/home/coorp/my_files
```

When un-archived, the directory structure will be:

- _/some_path/my_files_

And not:

- _/some_path/home/coorp/my_files_.

Fin.

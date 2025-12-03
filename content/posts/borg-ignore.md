+++
title = "Ignoring Directories When Backing up with Borg"
date = 2018-09-14T20:36:32Z
draft = false
hideToc = true
+++

Borg backup has a `--exclude-if-present` flag, which allows you to ignore a directory from being backed up, If it contains a certain 'tag' file.

So given the following directory structure, which we want to back up:

```
+ MyFiles
	+ dirA
		- file_one.pdf
		- file_two.pdf
	+ dirB
		- spreadsheet.xsl
		- image.png
```

If we want to ignore `dirB` from being backed up by borg, we have to create a tag file in that directory:

```
$ cd dirB
$ touch borg-ignore-dir
```

**Note:** the tag file does not have to be named `borg-ignore-dir`. You can name it however you like.

Now we can run borg backup as follows:

```
$ borg create --exclude-if-present borg-ignore-dir ~/borgs/MyFiles::BackupName MyFiles/
```

Only `dirA` will be backed up.

Useful Links:

[BorgBackup Homepage](https://www.borgbackup.org/)

Fin.

+++
date = "2017-02-20T21:42:26Z"
draft = false
title = "Raspberry Pi Slow SSH Fix"
hideToc = true

+++

If you SSH into your Raspberry Pi and have noticed a lag when typing characters into the terminal. Then the following fix may get rid of the lag (it worked for me!).

Log into your Raspberry Pi and type:

```
$ sudo nano /etc/ssh/sshd_config
```

At the bottom of the config file add:

```
UseDns no
```

Save the file, then restart sshd:

```
$ service ssh restart
```

Or better, reboot your Raspberry Pi:

```
$ sudo reboot
```

SSH'ing into your Raspberry Pi should no longer be slow after these steps. 🤞

Fin.

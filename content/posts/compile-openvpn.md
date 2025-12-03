+++
title = "Compiling OpenVPN on the Raspberry Pi 3"
date = 2019-12-15T14:41:12+01:00
draft=  false
hideToc = true
+++

Recently I needed to upgrade the version of OpenVPN on my Raspberry Pi 3 Model B. Below are the steps needed to do this, here we upgrade to OpenVPN version 2.4.8.

```
$ cd /tmp
$ wget https://swupdate.openvpn.org/community/releases/openvpn-2.4.8.tar.gz
$ tar xf openvpn-2.4.8.tar.gz
$ cd openvpn-2.4.8/
$ sudo apt-get install libssl-dev
$ sudo apt-get install liblzo2-dev
$ sudo apt-get install libpam0g-dev
$ ./configure --prefix=/usr
$ sudo make
$ sudo make install
```

Verify that the new version installed:

```
$ openvpn --version
OpenVPN 2.4.8 armv7l-unknown-linux-gnueabihf
[SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [MH/PKTINFO] [AEAD] built on May 28 2020
library versions: OpenSSL 1.1.1d  10 Sep 2019, LZO 2.10
Originally developed by James Yonan
Copyright (C) 2002-2018 OpenVPN Inc <sales@openvpn.net>
...
```

Hope this helps.

Fin.

+++
date = "2017-02-26T00:15:59Z"
draft = false
title = "Access Raspberry Pi Externally using ngrok"
hideToc = true

+++

In this blog post we will set up our Raspberry Pi so it will accessed using SSH from outside our home network. Below is a diagram of the architecture:

[ Home Network : Raspberry Pi ] <-- [ ngrok ] --> [ External Network ]

From our home network we will create a secure tunnel, through ngrok. Which we will then connect to from our external network. This will allow us to SSH into our Raspberry Pi, and manage it.

##### What is ngrok?

ngrok is a fantastic tool which allows you to create secure tunnels to localhost. So you can do things like expose a local server behind a NAT or firewall to the internet. See the [ngrok](https://ngrok.com/) homepage for more information.

Lets get started!

#### Step 1: Enable Passwordless SSH Access

You need to configure your Pi for SSH access. Follow the steps described in the following link (very carefully):

- [Passwordless SSH Access - raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)

By the end, you should be able to type:

```
$ ssh USER@Pi-IP-ADDRESS
```

And connect to your Raspberry Pi without a password prompt. For example:

```
$ ssh pi@192.168.0.42

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Feb 25 20:24:30 2017 from 192.168.0.42

pi@raspberrypi ~ $
```

No prompt for a password.

**Note**: the computer you generated the SSH keys on (and copied to the Pi). Is the computer you will be using to connect to your Pi, from the external network.

#### Step 2: Install ngrok on your Raspberry Pi

Go [here](https://ngrok.com/download) and follow the instructions to install ngrok on your Pi.

#### Step 3: Run ngrok on your Raspberry Pi

Run ngrok with the following options:

```
pi@raspberrypi ~ $ ngrok tcp 22
```

#### Step 4: Copy ngrok Host and Port

Once ngrok starts, it will display a _tcp://_ address on the forwarding line, for example:

> Forwarding tcp://123xyz0.tcp.ngrok.io:17684 -> localhost:22

Copy the host name _123xyz0.tcp.ngrok.io_ and the port _17684_.

**Note**: the host name and port will be different for you.

#### Step 5: Edit SSH config file

In step one you generated some ssh keys on your computer, and then copied them to the raspberry Pi. On the computer you generated the SSH keys on, add the following details to _~/.ssh/config_:

```
Host ngrok
        HostName 123xyz.tcp.ngrok.io
        IdentityFile ~/.ssh/id_rsa
```

Again, the host name will be different for you.

**Note**: we are using the same key we copied over to the Pi in step one, you can generate new SSH keys if you like.

#### Step 6: Access the Raspberry Pi from outside your network

Now with your computer **not** connected to your home network, type the command:

```
$ ssh pi@ngrok -p 17684
```

Change the username and port to match yours.

You should now be connected to your Raspberry Pi using SSH through ngrok.

Fin.

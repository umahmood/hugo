+++
title =  "Secure Tunnels with WireGuard"
date =  2020-03-23T22:11:03+01:00
draft = false
hideToc = true
+++

Wireguard is a secure networking tunnel, it can be used for VPNs, connecting data centers together over the Internet, or any place where you need to connect two or more networks together in a secure way.

Wireguard is open source and it is designed to be much simpler to configure than other tools such as OpenVPN or IPSec.

Below, we are going to connect two computers together:

```
Machine A IP address 192.168.0.40
Machine B IP address 192.168.0.41
```

First, we need to install WireGuard on each machine:

```
$ sudo apt install wireguard
```

Or if you are on macOS:

```
$ brew install wireguard-tools
```

On machine A and machine B run the following commands:

```
$ wg genkey | tee privatekey | wg pubkey > publickey
```

This will read privatekey from stdin and write the corresponding public key to publickey on stdout.

On machine A create the configuration file:

```
$ nano wg0.conf
```

And enter the following configuration data:

```
[Interface]
Address    = 10.0.0.40/24
PrivateKey = < copy & paste machine A's private key >
ListenPort = 58891

[Peer]
PublicKey  = < copy & paste machine B's public key >
AllowedIPs = 10.0.0.41/32
EndPoint   = 192.168.0.41:56991

# if behind firewall or NAT
PersistentKeepalive = 25
```

On machine B create the configuration file:

```
$ nano wg0.conf
```

And enter the following configuration data:

```
[Interface]
Address    = 10.0.0.41/24
PrivateKey = < copy & paste machine B's private key >
ListenPort = 56991

[Peer]
PublicKey  = < copy & paste machine A's public key >
AllowedIPs = 10.0.0.40/32
EndPoint   = 192.168.0.40:58891

# if behind firewall or NAT
PersistentKeepalive = 25
```

To start WireGuard, on both machines run the command:

```
wg-quick up ./wg0.conf
```

On machine A run:

```
ping 10.0.0.41
```

On machine B run:

```
ping 10.0.0.40
```

If you see output from the ping commands, success! you now have two machines with a secure tunnel between them.

To stop WireGuard, on both machines run the command:

```
wg-quick down ./wg0.conf
```

I hope this introduction to WireGuard helped you.

Fin.

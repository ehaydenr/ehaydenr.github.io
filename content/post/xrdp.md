+++
banner = "banners/xrdp-login.png"
#categories = []
date = "2016-02-28T17:04:44-06:00"
description = ""
images = []
menu = ""
tags = []
title = "Accessing Ubuntu with Xrdp"

+++

Motivation
===

RDP stands for Remote Desktop Protocol - a protocol developed by Microsoft. The protocol was built to allow users to gain graphical access to remote machines. Xrdp is an implementation of this protocol and can be used to access remote linux machines.

Quick Note
===

I have only been able to get Xrdp to work while using the Xfce desktop environment.

Install
===

    $ apt-get install xrdp

Wow we're done! Ok, let's see if we can access it.

Access
===

Installing xrdp is a piece of cake. Accessing your server may or may not be easy.

## My server is on my home network

With the server on your home network, you should be able to easily access it with remote desktop by just supplying it's IP address on your LAN. If you still can't access it, make sure port 3389 is open on the server. You can check which ports are listening on your machine with the `netstat` utility. Also, make sure the server is on! `service xrdp start`.

## My server is in my VPN

You may have lucked out and the directions are exactly those specified above for home networks. Connect to your VPN and try to access it. If you still can't access it, keep reading.

## My server is somewhere that I can SSH

If you can SSH into the machine, that's all you need to view your remote machine over RDP. This is all possible thanks to SSH tunnels.

OSX
---

    $ ssh -L 3389:localhost:3389 user@remote_machine

Load up remote desktop and access your machine at `localhost:3389`. If you are using Microsoft Remote Desktop, `3389` is the default port and you can just use `localhost`.

Windows
---

We can use [PuTTY](http://www.putty.org) to tunnel.

### Converting to .ppk
If you have a private key to access the machine, you will need to convert it with [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Open up PuTTYgen and click **Load**. Find your private key on your system and load it on in.
{{% figure src="/images/puttygen_load.png" %}}

Go ahead and **Save private key**. The new extension is `.ppk`.
{{% figure src="/images/puttygen_save.png" %}}

### Setting up PuTTY Session

There are several settings in your session that you will need to fill out. Here's the starting prompt. You will need to put in the IP of the machine you want to connect to.
{{% figure src="/images/putty_prompt.png" %}}

Let's put your SSH key in if you have one. Hit browse and find the `.ppk`.
{{% figure src="/images/putty_key.png" %}}

Last but not least, we will need to make sure we tunnel RDP! In **Source port** put `3400` and **Destination** put `localhost:3389`. Click **Add**.
{{% figure src="/images/putty_tunnel.png" %}}

### Fire up Remote Desktop Connection

Go ahead and start RDC and connect to `localhost:3400`. When asked for your username, use the one on the machine you're connecting to. You're done!

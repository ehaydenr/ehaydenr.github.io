+++
banner = "banners/samba_graphic.png"
date = "2016-02-27T15:07:43-06:00"
description = ""
images = []
menu = ""
tags = []
title = "Creating a Samba Network Share on Ubuntu"

+++

Motivation
===

Network storage is a great way to collaborate with people. Creating a network share will allow you to make your data available to a number of people with ease.

What is Samba?
===

Samba is an implementation of the SMB/CIFS protocol allowing Linux, OSX, and Windows to share across network storage seamlessly. Check out the organization's [site](https://www.samba.org). Also, the official Ubuntu guide is [here](https://help.ubuntu.com/lts/serverguide/samba-fileserver.html).

Setting up the Share
===

## Install

First things first, let's get the packages we need.

    $ apt-get install samba


Your configuration file is located at `/etc/samba/smb.conf`. Feel free to peruse the configuration file. A brief explanation of some of the configuration settings that has helped me is [here](https://www.samba.org/samba/docs/using_samba/appb.html).

## Add Our Share

At the bottom of the configuration file you will see examples of shares. There are a couple printer related shares. You can either comment those out or ignore them. Here's our share:

    [myshare]
      comment = Ubuntu File Server Share
      path = /srv/samba/share
      guest ok = yes
      read only = no

Let's take a look at these settings. `[myshare]` is the name of the share. If we access are share located at `127.0.0.1`, we would access this share via `smb://127.0.0.1/myshare`. The `comment` is simply the description of the share. We will see this comment later. `path` is where on your machine you would like to share. Make sure you set permissions on the directory appropriately to ensure it is publicly readable and writeable. `guest ok` means exactly what you think it does: users can access the share anonymously. `read only = no` also means exactly what you think it does: users may write. Many more options are available and you should check out the samba docs to see what you need. Here is a [book](https://www.samba.org/samba/docs/using_samba/toc.html) that's available has been fairly useful to me. The appendices are what I've looked at the most.

Alright, now that we've updated our configuration, we need to restart our server!

    $ service smbd restart

## Talking to the Share

Let's run a command using smbclient to list the shares that the guest user has access to on localhost. Here's what I get on my server. You should get something similar.

    $ smbclient -L localhost -U guest --no-pass

    Domain=[WORKGROUP] OS=[Unix] Server=[Samba 4.1.13-Ubuntu]

      Sharename       Type      Comment
      ---------       ----      -------
      IPC$            IPC       IPC Service (network-share server (Samba, Ubuntu))
      raw             Disk      Raw Data
      home            Disk      Home Shares
    Domain=[WORKGROUP] OS=[Unix] Server=[Samba 4.1.13-Ubuntu]

      Server               Comment
      ---------            -------
      NETWORK-SHARE        network-share server (Samba, Ubuntu)

      Workgroup            Master
      ---------            -------
      WORKGROUP            NETWORK-SHARE

If we want to go ahead and try to access the files on our share, we can access it again with smbclient. On my machine, I will access the `raw` share. This is what you used for `[myshare]` above.

    $ smbclient //localhost/raw -U guest --no-pass

    Domain=[WORKGROUP] OS=[Unix] Server=[Samba 4.1.13-Ubuntu]
    smb: \> ls
      .                                   D        0  Fri Feb 26 04:33:23 2016
      ..                                  D        0  Tue Feb 23 23:04:14 2016
      readme.txt                          A      144  Fri Feb 26 04:32:51 2016
      data.csv                            A  1162928  Thu Feb 25 22:29:11 2016

        64491 blocks of size 8388608. 61204 blocks available

Notice `smb: \> ls`. Once I got the smb prompt, I asked it to the list the files for me. If you write `smb: \> help` you get the following helpful list of available commands.

    smb: \> help
      ?              allinfo        altname        archive        backup
      blocksize      cancel         case_sensitive cd             chmod
      chown          close          del            dir            du
      echo           exit           get            getfacl        geteas
      hardlink       help           history        iosize         lcd
      link           lock           lowercase      ls             l
      mask           md             mget           mkdir          more
      mput           newer          notify         open           posix
      posix_encrypt  posix_open     posix_mkdir    posix_rmdir    posix_unlink
      print          prompt         put            pwd            q
      queue          quit           readlink       rd             recurse
      reget          rename         reput          rm             rmdir
      showacls       setea          setmode        stat           symlink
      tar            tarmode        timeout        translate      unlock
      volume         vuid           wdel           logon          listconnect
      showconnect    tcon           tdis           tid            logoff
      ..             !

Let's use `put` to put a file on our server. The help entry for put can be accessed like this.

    smb: \> help put
      HELP put:
        <local name> [remote name] put a file

Let's add our file.

    $ touch helloworld.txt
    $ smbclient //localhost/raw -U guest --no-pass

    Domain=[WORKGROUP] OS=[Unix] Server=[Samba 4.1.13-Ubuntu]
    smb: \> put helloworld.txt helloworld.txt
    putting file helloworld.txt as \helloworld.txt (0.0 kb/s) (average 0.0 kb/s)
    smb: \> ls
      .                                   D        0  Sat Feb 27 22:59:45 2016
      ..                                  D        0  Tue Feb 23 23:04:14 2016
      readme.txt                          A      144  Fri Feb 26 04:32:51 2016
      data.csv                            A  1162928  Thu Feb 25 22:29:11 2016
      helloworld.txt                      A        0  Sat Feb 27 22:59:45 2016

        64491 blocks of size 8388608. 61204 blocks available

Awesome. Now let's get rid of it.

    smb: \> rm helloworld.txt
    smb: \> ls
      .                                   D        0  Sat Feb 27 23:02:05 2016
      ..                                  D        0  Tue Feb 23 23:04:14 2016
      readme.txt                          A      144  Fri Feb 26 04:32:51 2016
      data.csv                            A  1162928  Thu Feb 25 22:29:11 2016

        64491 blocks of size 8388608. 61204 blocks available

Accessing the Share from Other Machines
===

Finding your share shouldn't be too difficult if you're on the same network as it. On linux, go to your file manager and there is most likely an option to explore your network. Click it and you should see your server. If you're on OSX, `Finder > Go > Connect to Server` will give you the following prompt.

{{% figure src="/images/connect_to_server.png" %}}

## Important Note
If your server is on the same network as you, you should be fine. If it is on another network, you will likely need to jump through a couple more hoops to connect to it. DO NOT open up your share to the rest of the world if you do not have proper security configured. The samba share described in this post does not have ANY security. Don't bypass your firewall and forward ports to access this share unless you know what you're doing.

Cheat Sheet
===
Objective | Command
--- | ---
Restart Server | `service smbd restart`
List Shares | `smbclient -L <machine-ip> -U <user>`
Access Share | `smbclient //<machine-ip> -U <user>`


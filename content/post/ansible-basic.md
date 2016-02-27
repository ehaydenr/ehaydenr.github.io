+++
banner = "banners/ansible-logo.png"
categories = ["Orchestration"]
date = "2016-02-25T17:32:58-06:00"
description = "Getting started with Ansible."
images = []
tags = ["Ansible"]
title = "Intro to Ansible"

+++

Motivation
===

Managing a one or two machines is a fairly easy task. Installing software, managing users, mounting network drives, etc. can be done by manually spawning a shell to each machine and running whatever commands we need to on each machine individually. While it doesn't seem too bad for a couple machines, what if we want to manage many machines? If I have to gather information about all 1000 of my machines, I don't want to have to manually spawn a shell in each. This would be unrealistic.

Luckily, there are nice people out there that work on management and orchestration tools. Ansible is one such tool.

Installation
===

The official install doc is [here](http://docs.ansible.com/ansible/intro_installation.html).

    $ brew install ansible


Basics
===

## Creating a hosts file

The hosts file is where you put all the machines you are interested in working with. You need two things: group name and address. You can use IP addresses or use DNS. Here's an example:

    [mygroupname]
    192.168.1.1
    192.168.1.2
    example.com

    [someothergroup]
    192.168.1.1
    some.other.machine.com

Three machines are all associated with the "mygroupname" group. Two other machines are associated with "someothergroup" group.

## How to we setup access to these machines?

We need to make sure a couple of things are in place before we can get started running commands. First, we need to have an account on each of the machines. If you're setting things up for the first time, you might want to put an account with the same name on each machine you want access to. If you're trying to connect to an existing system that doesn't have an account on each machine, you're going to have to get a little more involved with the settings. For example, you might have to ammend your hosts file like such.

    [mygroupname]
    192.168.1.1 ansible_ssh_user=someuser ansible_ssh_private_key_file=/path/to/key.pem
    192.168.1.2 ansible_ssh_user=someotheruser ansible_ssh_pass=password
    example.com ansible_ssh_user=joe ansible_ssh_pass=password

    ...

What I like to do is setup my `~/.ssh/config` like such:

    Host *.domain.com
      User admin
      IdentityFile ~/.ssh/admin.pem

If all my machines are subdomains of domain.com, whenever I ssh (and whenever ansible does) it will use the user `admin` and the private key `~/.ssh/admin.pem`. This way I don't have to specify my username in my hosts file. Another great option is to use ansible group variables. You can learn about the hosts file (inventory file) and connecting to your machines [here](http://docs.ansible.com/ansible/intro_inventory.html).

## Our first command

Let's run ansible with the command module to `echo hello` on all of our machines.

    $ ansible mygroupname -i hosts -m command -a "echo hello"

If all goes well, you should get something like this:

    192.168.1.1 | SUCCESS | rc=0 >>
    hello

    192.168.1.2 | SUCCESS | rc=0 >>
    hello

    example.com | SUCCESS | rc=0 >>
    hello

Notice in our command to ansible `mygroupname` was the first argument. This argument tells ansible which group we wish to execute our ansible command on. If we instead did `ansible someothergroup ...` it would run on the machines listed in the other group. If you want to run the command on all the groups, you specify `all` as the group.

## Getting the uptime of each machine

Here's an example of a command that's a little more useful

    $ ansible somegroup -i hosts -m command -a "uptime"

    192.168.1.1 | SUCCESS | rc=0 >>
     22:56:54 up 2 days, 21:28,  1 user,  load average: 0.03, 0.03, 0.05

    192.168.1.2 | SUCCESS | rc=0 >>
     22:56:54 up 2 days, 21:28,  1 user,  load average: 0.00, 0.01, 0.05

    192.168.1.3 | SUCCESS | rc=0 >>
     22:56:54 up 2 days, 21:28,  1 user,  load average: 0.00, 0.01, 0.05

    192.168.1.4 | SUCCESS | rc=0 >>
     22:56:54 up 2 days, 21:28,  1 user,  load average: 0.03, 0.03, 0.05

## Modules

Ansible has a ton of modules that are useful to common orchestration and management tasks. [Here](http://docs.ansible.com/ansible/list_of_all_modules.html) is a potentially intimidating list of them.

Modules are awesome. They are very powerful and aren't going to be covered in this post. Modules can be used on the command line, but most examples of them will be within ansible plays. Another feature of ansible is *plays*. Using ansible *plays* within `anisble-playbook` will be a topic for another post. The offical docs are [here](http://docs.ansible.com/ansible/playbooks.html).

Here are some that I've found useful.

Module Name | Summary
--- | ---
[apt_key](http://docs.ansible.com/ansible/apt_key_module.html) | Add or remove apt key
[apt_repository](http://docs.ansible.com/ansible/apt_repository_module.html) | Add or remove apt repository
[apt](http://docs.ansible.com/ansible/apt_module.html) | Manage apt packages
[user](http://docs.ansible.com/ansible/user_module.html) | Manage user accounts
[authorized_key](http://docs.ansible.com/ansible/authorized_key_module.html) | Add or remove ssh keys for user accounts
[mount](http://docs.ansible.com/ansible/mount_module.html) | Control mount points
[file](http://docs.ansible.com/ansible/file_module.html) | Set attributes of files

Cheat Sheet
===

Objective | Command
--- | ---
Uptime | `ansible all -i hosts -m command -a "uptime"`
Who's logged in? | `ansible all -i hosts -m command -a "who"`

Resources That Helped Me
===
* [Watch this first](https://youtu.be/xew7CMkL7jY)
* [Watch this second](https://youtu.be/2jXHxkLBOHg)

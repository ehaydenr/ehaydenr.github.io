+++
banner = "banners/ansible-playbook.jpg"
categories = ["Orchestration"]
date = "2016-02-26T17:18:47-06:00"
description = "Getting started with Ansible Playbook"
images = []
menu = ""
tags = ["Ansible"]
title = "Intro to Ansible Playbook"

+++

Image From: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-create-ansible-playbooks-to-automate-system-configuration-on-ubuntu)

Before we get started, if you haven't already, check out [Intro to Ansible]({{< relref "ansible-basic.md" >}}).

Motivation
===

Inlining Ansible commands on the CLI is a pain. Also, what if we want to do a bunch of commands at once? Ansible's solution is playbook.

Welcome to the world of playbooks
===

Playbook is essential to achieving a good administration workflow. If you're inlining a lot on the command line, you're doing too much work; let playbook do the work for you.

## Sample Play

The *play* is a set of *tasks* and with configurations to run on your infrastructure. Let's take a look.

    ---
    - hosts: all
      become: yes
      become_user: root
      tasks:
        - name: add to sudoers
          user: name=eric state=present groups="sudo" append=yes

Above is a play with only a single task. As we add more tasks, you'll see the benefit of plays. There are a bunch of configurations you can add to your play. For example, `become` tells ansible that you want to run the following tasks a different user. `become_user: root` tells ansible we want to become the root user. If you're curious, [become documentation](http://docs.ansible.com/ansible/become.html).

Under tasks, we have a list of size 1. The `name` attribute simply gives the task a name. `user` specifies that we are using the `user` module. If we go look at the module [documentation](http://docs.ansible.com/ansible/user_module.html), we see a number options we have.

### Wait.. what's the difference between a play and a playbook?

In our Sample Play, you can see a playbook file. This playbook only consists of one play which consists of only one tasks. Essentially, plays are made up of tasks and playbooks are made up of plays. A playbook lives in one file.

## Another Play

Keeping packages up to date with Ansible is a great use case. Let's see what the file looks like.

    ___
    - hosts: all
      become: yes
      become_user: root
      tasks:
          - name: update package lists
            apt: update_cache=yes

          - name: upgrade packages
            apt: upgrade=yes

If you want to see the specifics of `update-cache` and `upgrade` go check out the [docs](http://docs.ansible.com/ansible/apt_module.html). You should be referring to them.

Let's run it!

    $ ansible-playbook plays/my_play.yml -i hosts --ask-sudo-pass

## Using Variables in a Playbook

What happens if we want to install a bunch of packages at the same time? Playbook variables allow us to use loop-like constructs inside our playbook. Let's take a look.

    ---
    - hosts: analyst
      become: yes
      become_user: root
      vars_files:
        - my_vars.json
      tasks:
          - name: install packages
            apt: name={{item}} state=latest
            with_items: packages

Variables File - `my_vars.json`

    {
      "packages": [
        "texlive",
        "texstudio",
        "git",
        "python-pip",
        "xfce4",
        "iceweasel",
        "r-base",
        "r-base-dev",
        "xrdp",
        "smbclient",
        "zsh",
        "cifs-utils"
      ]
    }

Using `with_items` allows us to install all of these packages with just one task! `with_items` is useful in many situations.

### Variables on CLI

In the example above, I specified variables through the `var_files:` option. If we want to add variables at the time of running the command without having to rewrite a play, we can specify `--extra-vars="@my_extra_vars.json"`

Cheat Sheet
===

Objective | Command
--- | ---
Limit hosts affected | `-l groupname`
Ask sudo password for Become | `--ask-sudo-pass`
Extra vars | `--extra-vars="@my_extra_vars.json"`

+++
date = "2016-02-24T20:41:45-06:00"
title = "Getting Set Up With Hugo"
banner = "banners/desk.jpg"
tags = ["Hugo"]
categories = ["Web"]

+++

Motivation
===

There are certain instances where statically generating your website is a great idea. There are tools out there like [WordPress](https://wordpress.org/), [Drupal](https://www.drupal.org/), and [Joomla](https://www.joomla.org/) that provide much functionality that can be overkill. The difference between [Hugo](https://gohugo.io/) and these Content Management Systems (CMS) is static vs. dynamic content delivery. While it does have its restrictions, static site delivery has some pretty great performance benefits. If the server doesn't have to query a database and build the page, it will deliver the user content much quicker. The idea is that we do all the work in building the site once and deliver that product many times.

Installation
===

Installation steps are officially documented [here](https://gohugo.io/overview/installing/).

Quickstart guide is located [here](https://gohugo.io/overview/quickstart/).

Cheat Sheet
===

Objective | Command
--- | ---
Create new site | `hugo new site path/to/site`
Create post | `hugo new post/example.md`
Run server | `hugo server`
Run server with theme | `hugo server --theme=<theme name>`
Run server and watch files for updates | `hugo server -w`

Notes
===

## Get All Themes
[Steve Francia](https://github.com/spf13), the founder lof the Hugo project, maintains a repository of all hugo themes [here](https://github.com/spf13/hugoThemes). You can conveniently download all the themes and add them to your poject.

    $ cd /path/to/project
    $ git clone --depth 1 --recursive https://github.com/spf13/hugoThemes.git themes


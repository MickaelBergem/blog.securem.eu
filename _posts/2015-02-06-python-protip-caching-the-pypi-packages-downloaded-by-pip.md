---
layout: post
title: "Python ProTip : caching the PyPi packages downloaded by PIP"
description: "It is possible to cache the PyPi packages downloaded by PIP, to make your builds more fast"
category: Tips and tricks
tags: [python, pip]
---
{% include JB/setup %}

**WARNING: this method is deprecated since [pip 6.0](https://pip.pypa.io/en/stable/news/), as caching is now enabled by default**

***

## The How

Add the following lines in `~/.pip/pip.conf` (which may not exist) :

<script src="https://gist.github.com/MickaelBergem/9c32a9093a4009042d81.js"></script>

## The Why

A few weeks ago, I was still working (as an intern) at Theodo as an Agile Web Developer, deploying my source code several times a day on a staging server (and pushing to production at least once a week). This is **continous deployment**.

The deployment script, written with [Ansible](http://www.ansible.com/home), was creating a whole new build of the project in a new folder (then linked the "current" folder to this "release" build):

* downloading and building the assets for the frontend: Bootstrap, Font Awesome, etc. (with Bower) with `npm install`
* installing the Python backend part by creating a new virtualenv inside the "release" folder, and then running `pip install`

At each and every deployment, all the python packages needed were downloaded, then installed, which is time-consuming. Caching allow us to gain time on the downloading part.

**Note** : To gain time on the installation part, it is also possible to use a common `virtualenv` for all the releases, but there is the risk of suppressing a vital package in the `requirements.txt` file without noticing (only builds on brand new machines will fail). I also experienced a similar issue with the frontend part (`npm install` downloads and install the JS packages in `./node_modules`) where a package suddenly stopped working in the way we were using it : we noticed it immediately and fixed our code, instead of facing this bug during a production deployment.

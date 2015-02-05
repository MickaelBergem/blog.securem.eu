---
layout: post
title: "Unfollower : minimal Python script to track who unfollowed you"
description: "Find out who unfollows you"
category: projects
tags: [python, script, twitter]
---
{% include JB/setup %}

You may have noticed that some Twitter accounts are massively following other people, adding several dozens of accounts each day, in order to be followed back and to increase their number of followers.
In the beginning, I followed some of them as they were matching some of my interest fields (entrepreneurship, web, new technologies...), but I then discovered that as soon as I followed them, some of them were unfollowing me.

I guess this is to keep a "decent" number of "following" accounts (to not have 100x more accounts followed by you than following you).

I also guess some of my followers either don't find me interesting, or don't like me posting French content (which I understand), but this is another question and it won't be solved by a script ;)

So I created [this Python script](https://github.com/MickaelBergem/unfollower), which basically stores in a SQLite file (but you can configure any SQLAlchemy-compatible backend) the list of you follower and poll every hour the Twitter API to check if somebody unfollowed you.

There are already many online tools which tracks unfollowers, with nice charts like [unfollowerstats.com](http://unfollowerstats.com/), and even paid plans : [who.unfollowed.me](http://who.unfollowed.me/plans-pricing). My script is not intended at all as a replacement of these web interfaces, but is a base to bind custom notifications or callbacks.

<a href="https://github.com/MickaelBergem/unfollower" class="bigbutton bigbutton-center">Check the code !</a>

### Technical details

* SQLAlchemy allows you to use a lot of database backend if you need / want to. By default, SQLite is used for its simplicity.
* the Python script is ~60 lines and is fully customizable, whenever you want to adapt the notification system
* using the Twitter API is really easy thanks to the `python-twitter` package
* in order to use the API, you will need [to create tokens and consumer key](https://github.com/bear/python-twitter#api)

The installation is straightforward, [just follow the instructions](https://github.com/MickaelBergem/unfollower#installation).

I recommend using `python-twitter` (instead of the `twitter` PyPi package), because I find it simpler and easier to use.

### Demonstration

At the first start, Unfollower will detect your followers and add them into the database.

![First launch](/assets/illustrations/unfollower-first.png "First launch of Unfollower")

Then, whenever someone unfollow you, a line will be printed :

![Unfollower detected](/assets/illustrations/unfollower-found.png "Unfollower detected")

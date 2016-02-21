---
layout: post
title: "Tracking events with Piwik"
description: "Piwik is the leading open source web analytics platform, in this post I will explain how to track specific events triggered on the client side"
category: Tips and tricks
tags: [opensource, analytics, Piwik]
---
{% include JB/setup %}

### Piwik ?

![Piwik logo](/assets/illustrations/pw_logo.png)

Piwik is [the leading open source web analytics platform](http://piwik.org/what-is-piwik/), and offers many advantages compared to Google Analytics (that I have used for years before switching to Piwik):

* *free and open-source software*: although Google Analytics is free too, Piwik is open-source and will always remain free and hackable
* you can *install it on your own server*: no data is sent to a third party and *you stay the master of the data*, this is especially important for people (like me) thinking it is important not to share all your (and your customers') data with Google or advertising companies
* you have access to the data: you can use APIs, custom plugins or even manually execute SQL queries to build custom reports or bind a CRM to it
* the *community* is big, and the development is fast, as shown by the Github activity graphs :
[![Commits graph](/assets/illustrations/pw_github_commits.png)](https://github.com/piwik/piwik/graphs/commit-activity)
[![Contributions graph](/assets/illustrations/pw_github_stats.png)](https://github.com/piwik/piwik/graphs/contributors)
* Piwik appears to be lighter and to load faster (haven't done the test myself)

The main advantage I find in Piwik is **you are in control**, if you need to remember one thing. Note that features may be more developed and advanced in Google Analytics than in Piwik, [this blog post](http://www.zenincognito.com/piwik-vs-google-analytics-a-detailed-review/) compares both.

### Tracking events

Now, what ?

Piwik and Google Analytics only track page views by default (who was where, when, how), but not what happened inside the page. And you may want to track more precisely the behavior of your visitors, for many different reasons :

* who clicked on which button, and triggered what action inside the page ?
* who read the text to the end (footer) ?
* which photos are the most hovered on [the "about / team" page of your website like this one](http://geoponts.securem.eu/about) ? This one is a very nice idea for curious people like me (who is the most charismatic in your team ?)
* when you have dynamic content : who visited tabs, accordions ?

Every time you need to track what happened on the client side, [Piwik can track it](http://piwik.org/docs/event-tracking/), and this is really easy. All you have to do is to call the following piece of Javascript :

    _paq.push(['trackEvent', 'CategoryName', 'ActionName', 'ActionValue']);

For example, in the case of the team pictures :

    $(function () {
        $('.team-member > img').on("mouseover", function () {
            _paq.push(['trackEvent', 'Behavior', 'HoverPhoto', this.id]); }
        )
    })

Here is the result :

![Event triggered Piwik](/assets/illustrations/pw_events.png)

Note that you can also pass an optional numeric value for the action :

    _paq.push(['trackEvent', 'Posts', 'RatingPost', 'PostName', 8.5]);


### Tracking events directly from you application or server

It is also possible, but I haven't tried it yet, to trigger events from your backend, with [the Tracking HTTP API](http://developer.piwik.org/api-reference/tracking-api) : register when emails are sent, when an error occurs, or anytime something happens inside the backend !

And if you want to join the community, [head to the Piwik Developer Zone](http://developer.piwik.org/) ! :)
<link rel="stylesheet" href="/assets/highlight.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.4/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>

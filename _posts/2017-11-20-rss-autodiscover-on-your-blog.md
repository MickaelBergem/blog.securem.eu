---
layout: post
title: "RSS autodiscover on your blog"
description: "How to add RSS autodiscover to your blog or website"
category: "Tips and tricks"
tags: [Publishing,HTML]
image: /assets/illustrations/rss.png
---
{% include JB/setup %}

Last week I read an interesting article about why RSS was still so useful in 2017, and it reminded me how much I was using RSS feeds and aggregators a few years ago.

<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr">&quot;RSS: there&#39;s nothing better&quot; - or why old geeky things are still very useful for everyone<a href="https://t.co/2tW6998dg2">https://t.co/2tW6998dg2</a></p>&mdash; Suixo (@msuixo) <a href="https://twitter.com/msuixo/status/929297267261083648?ref_src=twsrc%5Etfw">11 novembre 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

But last week, Mozilla also released Firefox Quantum with a built-in "Subscribe to this page" RSS button (I suspect it had been there for a very long time and I just didn't see it before).

![RSS autodiscover feature](/assets/illustrations/rss.png)

And then, horror! The RSS feed for my blog is not detected! ... Oh ok I need to add something. Easy.

To signal to browsers such as Firefox that **your blog or your website has a RSS feed they can subscribe to**, you need to add the following code (in your `<head>`):

```html
<link rel="alternate"
    type="application/rss+xml"
    title="My awesome blog's RSS feed"
    href="/rss.xml" />
```

And here you go, the button now allows your users to subscribe to your feed. Aggregator will also now "see" your content and make it discoverable to other readers.

You can also add multiple tags if you have multiple feeds.

That was a quick fix to a problem many website have today, so if you haven't already, add this nice little chunk of code to your pages :)

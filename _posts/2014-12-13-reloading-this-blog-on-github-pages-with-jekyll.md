---
layout: post
title: "Reloading this blog on GitHub Pages with Jekyll"
description: "I am migrating my old blog (created in 2008 !) on GitHub Pages, using Jekyll. Learn how and why I did that."
category: News
tags: [GitHub Pages, Publishing, SecureM]
---
{% include JB/setup %}

Hey there ! It has been a long time since I have posted anything on my old *Dotclear* blog. I used to write things for myself, to share the little knowledge I gained, interesting websites, and good security practices.

It was written in French, my mother tongue, and was left abandoned, but I felt that there would be a wide variety of topics I would love to talk about, and I wanted a new start. To be able to share with more people across the world, I also wanted to write the content in English. Sorry for the misspellings, mistakes, and other weird sentences, don't hesitate to post a comment to correct me !

I wanted a fast (my previous hoster, [Olympe Network](http://www.olympe.in/) , is doing a wonderful work to provide free, ad-free, and opensource hosting to ~50000 users, but the performances weren't good enough), simple, and customizable blog.

## Why Github Pages and Jekyll

First, *Github Pages* is a really simple (free) way to publish static code : just push code in the `gh-pages` branch of a repository and it will become available at http://yourusername.github.io/repositoryname/ ! GitHub pages are using [Fastly CDN](https://www.fastly.com/) to [improve loading times](https://github.com/blog/1715-faster-more-awesome-github-pages), which makes it fast.

But as only static (HTML for instance) pages can be committed, you will need [*Jekyll*](http://jekyllrb.com/) to manage the global layout, structure and formatting :

* Ability to write content *in markdown or textile* in your favorite text-editor.
* Ability to write and preview your content via localhost.
* No internet connection required (to edit and preview the content).
* Ability to publish via git.
* Ability to host your blog on a static web-server, like GitHub Pages.
* No database required.

The *How ?* part is coming soon !

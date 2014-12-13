---
layout: post
title: "Jekyll Rake cheatsheet"
category: "Tips and tricks"
tags: [Publishing, Markdown]
---
{% include JB/setup %}

When you create blog posts or pages with Jekyll, here are the Rake commands to remember :

    # Only the title parameter is mandatory
    rake post title="Jekyll Rake cheatsheet" tags=['Publishing', 'Markdown'] category="Tips and tricks" [date="2014-12-13"]

    rake page name="about.html"
    rake page name="subcategory/niceformatting/index.html"

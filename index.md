---
title: A Student Hacker's Life
layout: page
---
{% include JB/setup %}

Welcome to my blog.

I am Mickaël, aka [Suixo](https://twitter.com/msuixo), a French developer, entrepreneur and hacker.
Learn more about me [on my personal webpage](http://me.securem.eu/).

***

Here are my latest blog posts :

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

---
title: A Student Hacker's Life
layout: page
---
{% include JB/setup %}

Welcome on my blog.

I am Mickaël, aka [Suixo](https://twitter.com/msuixo), a French student and hacker. Learn more on me [on my personal webpage](http://me.securem.eu/).

***

Here are my last blog posts :

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

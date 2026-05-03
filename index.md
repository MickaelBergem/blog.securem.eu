---
title: A Student Hacker's Life
layout: page
---
{% include JB/setup %}

Welcome to my very old blog. I don't post here anymore.

I am a French software engineer, entrepreneur and hacker.

***

Here are my latest blog posts :

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

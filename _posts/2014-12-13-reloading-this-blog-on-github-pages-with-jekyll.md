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

## How ?

I often do **"bang-documentation"**, meaning that **I only provide the key steps to achieve the goal**, assuming that you are able to find more detailed documentation by yourself if needed. If a part is unclear, though, please leave me a comment and I will fix it !

I used [Jekyll Bootstrap](http://jekyllbootstrap.com/), which use pre-built HTML Bootstrap pages instead of Liquid templating : directly editing HTML seems to me easier than learning a new templating language.

Here is the process I followed to build this blog, you can find [the original quickstart guide here](http://jekyllbootstrap.com/usage/jekyll-quick-start.html) :


1. Fork [https://github.com/plusjade/jekyll-bootstrap.git](https://github.com/plusjade/jekyll-bootstrap.git) or clone/push it into a new public / private repository

        git clone https://github.com/plusjade/jekyll-bootstrap.git jekyll-blog
        cd jekyll-blog
        git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
        git push origin master:gh-pages

2. Add a CNAME file and entry in your DNS zone

        echo "blog.securem.eu" > CNAME

        git add CNAME && git ci -m "Add CNAME file" && git push
        # You will also need to edit your DNS zone to point the right subdomain to your github pages (username.github.io)

4. Run `gem install --user-install jekyll` on your computer to install Jekyll

5. Configure `/_config.yml` to your needs (for example, the blog's name or a Piwik / Google Analytics tracking ID)

6. Launch the built-in webserver with `jekyll serve` then head to [http://127.0.0.1:4000/](http://127.0.0.1:4000/) (will autoreload the server when you create/edit content, but it doesn't watch the `/_config.yml` file)

7. Create your first blog post with

        rake post title="My first blog post !"

    Then edit the newly created file in `/_posts/`.

8. Choose a nice theme from [http://themes.jekyllbootstrap.com/](http://themes.jekyllbootstrap.com/) and install it with

        rake theme:install git="https://github.com/jekyllbootstrap/theme-the-minimum.git"

9. Change the landing page in `/index.md`. Pages can be placed anywhere (root directory or subdirectory to class them) but must contain the YAML Front-matter header.

10. Commit, push, and enjoy !


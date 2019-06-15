---
layout: post
title: "Protip: don't use 'yarn install' on your CI pipeline"
description: "Instead of using `yarn` or `yarn install` to install your JS dependencies, use `yarn install --frozen-lockfile` instead - it will fail if the lockfile is outdated."
category: "Tips and tricks"
tags: ['development','testing','best practices']
image: /assets/illustrations/frozen-lockfile.png
permalink: /protip/2019/06/14/protip-dont-use-yarn-install-on-your-ci-pipeline/
---
{% include JB/setup %}

I recently discovered the [`--frozen-lockfile`
parameter](https://yarnpkg.com/lang/en/docs/cli/install/#toc-yarn-install-frozen-lockfile) to `yarn
install`, while randomly reading `yarn`'s documentation <small>(yes I'm weird)</small>:

```bash
# Don’t generate a yarn.lock lockfile and fail if an update is needed.
yarn install --frozen-lockfile
```

This is super useful on a CI pipeline to **make sure we developers don't forget to update the lockfile
when we add a dependency**. In theory, it should never happen but sometimes people forget to commit
some parts of the changes - so using the `--frozen-lockfile` will save you a new commit or a new PR
when it happens.

So whatever CI pipeline you are using, from CircleCI to a self-hosted Jenkins server, that's one
more way of catching quality issues before they get merged ;)

### It's also available in other languages:

If you are also using Ruby for some of your services like we do at [Akido
Labs](https://angel.co/company/akidolabs), bundler has a similar option:

```bash
bundle install --frozen
```

Python's pipenv doesn't look like it has a similar option yet, though. Please let me know if other
languages are offering the same feature, I'll be happy to add them to this post.

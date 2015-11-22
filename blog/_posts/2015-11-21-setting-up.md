---
layout: post
title:  Setting up the github jekyll workflow
date: 2015-11-21 15:07:04
published: true
tags: [example1, example2]
---

**Note that this is obsolete. I am following the RMarkdown workflow now.**

# Blogging on github?

I've turned on the github pages for my github account. Now that I
have a static site, I would like to blog with jekyll.

Following the official guide here https://help.github.com/articles/using-jekyll-with-pages/

- Checked to make sure Ruby is up to date `ruby --version` >2.0

- Install bundler `sudo gem install bundler`

- Go to my github page repo `garyfeng.github.io`, created a file `Gemfile` with
the following content

```
source 'https://rubygems.org'
gem 'github-pages'
```

- CD into the repo, did `bundle install`. This took 5 minutes to install everything

- `jekyll new blog`

```
Gary-MBA:garyfeng.github.io garyfeng$ jekyll new blog
New jekyll site installed in /Users/garyfeng/github/local/garyfeng.github.io/blog.
```

- then `cd blog `. Did not do `git init` as github instruction said, because that
would make it impossible to sync given that my blog is inside the repo

- serve: `kekyll serve  --watch`, and go to http://localhost:4000/

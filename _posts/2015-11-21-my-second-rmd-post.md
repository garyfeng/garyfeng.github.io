---
layout: post
title:  My second Rmd post
date: 2015-11-21 23:38:54
published: true
tags: [example1, example2]
---

Well, I thought I disabled XCode from automatically popping up. It still does.

Secondly, this workflow automatically adds `/knitr-jekyll` before the URL for figures, resulting something like `/knitr-jekyllfigure/...`. This was done at the knitr from Rmd to md. It's in the md.

Then I did some test, and changed the `baseurl: ""` to `baseurl: "/"` in `_config.yml`, the URL is fixed. But doing so would ruin the rest of the pages -- the "about" page becomes `http://about/`.

Now I am going back, and just have to manually do this.


{% highlight r %}
plot(1:10)
{% endhighlight %}

![plot of chunk unnamed-chunk-1](/figure/source/my-second-rmd-post/2015-11-21-my-second-rmd-post/unnamed-chunk-1-1.png) 

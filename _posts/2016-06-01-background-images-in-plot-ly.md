---
layout: post
title:  ConferoAnalytics, Background Images in Plot.ly
date: 2016-06-01 15:11:32
published: true
tags: [viz, python, javascript]
---

We have developed an open-source eye-tracking library [Confero](https://github.com/EducationalTestingService/Confero). Now it's time to finish the [ConferoAnalytics](https://github.com/EducationalTestingService/Confero). 

R or Python? Well, we are constrainted by the fact that Confero saves data in HDF5 files. The only R library I can find on CRAN is [H5](https://cran.r-project.org/web/packages/h5/), which [does not compile on my mac](https://www.google.com/search?q=error%3A+%27h5c%2B%2B%27+does+not+seem+to+be+installed+on+your+platform.&oq=error%3A+%27h5c%2B%2B%27+does+not+seem+to+be+installed+on+your+platform.&aqs=chrome..69i57j69i58.229j0j7&sourceid=chrome&ie=UTF-8), whereas I know Python/Pandas supports HDF5. So that's that, at least for now.

Next up, what visualization framework to use? Florian and I have developed some R functions that work well ... well enough fro publications, but not interactive. It's also a hack to get ggplot to work with background images, etc. 

I have been moving toward [plot.ly]() since a few months ago when it became open-source. According to [this blog post](http://slides.com/jackparmer/deck-7/fullscreen) you can do overlays, but it is not supported as a native feature and it takes a lot of tweaking to get the iframe alight right ... come on, Plot.ly. 


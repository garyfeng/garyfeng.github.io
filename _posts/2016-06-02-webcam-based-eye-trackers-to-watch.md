---
layout: post
title:  Webcam-based eye-trackers to watch
date: 2016-06-02 08:48:13
published: true
tags: [eye-tracking, multimodal]
---

Several new papers on webcam-based eye-tracking:

- [PACE](https://www.researchgate.net/publication/299490836_Building_a_Personalized_Auto-Calibrating_Eye_Tracker_from_User_Interactions), CHI16, a desktop application that uses mouse movements to recalibrate gaze estimation, based on assumptions about where mouse movements and gaze are most likely to align.

- [TurkerGaze](http://arxiv.org/abs/1504.06755), including [supplemental materials](http://turkergaze.cs.princeton.edu/supp.pdf) that includes some results on the mean-shift methods for fixation detection. [Github repo](https://github.com/PrincetonVision/TurkerGaze). Aiming to estimate visual saliency. 
- [WebGazer](http://jeffhuang.com/Final_WebGazer_IJCAI16.pdf), IJCAI16, from [Jeff Huang](http://jeffhuang.com/)'s group at Brown, with online demo at [http://webgazer.cs.brown.edu/](http://webgazer.cs.brown.edu/)

Essentially the same architecture here -- face tracking ==> getting the eyes ==> eye feature extraction ==> use ridge regression or other methods to calibrate

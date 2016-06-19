---
layout: post
title:  Jupyter Notebook unit testing
date: 2016-06-19 00:16:40
published: true
tags: [python, programming]
---

Thinking how to develop a python library for PDiA but in Jupyter notebooks where I can actually document using literate programing. I'd like to keep the code and the unit test in the same notebook, so that i can test when I want, but also can run a unittest later.

So far the solution I like the most is [https://github.com/JoaoFelipe/ipython-unittest](https://github.com/JoaoFelipe/ipython-unittest) which adds 3 ipython cell magics that make a lot of sense. 

Other solutions at the top of google search dont seem to fit what I need. 

- [https://zonca.github.io/2014/09/unit-tests-ipython-notebook.html](https://zonca.github.io/2014/09/unit-tests-ipython-notebook.html) requires `test*.ipynb` that are separate from the actual code.

- [http://docs.python-guide.org/en/latest/writing/tests/](http://docs.python-guide.org/en/latest/writing/tests/) is a general intro to python unittesting

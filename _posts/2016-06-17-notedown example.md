---
layout: post
title:  Notedown Example
date: 2016-06-17 15:32:34
published: true
tags: [programming]
---

The following is an output, from within a Jupyter Notebook. 

It looks great on github, viewed as a markdown file. However, code blocks look terrible on the Jekyle blog site. The problems seem to have to do with the failure to process the ` ```{.python .input  n=53}` code. 


----

# Parsing JSON strings in Pandas

Gary Feng,
Princeton, NJ
May, 2016

<h1 id="tocheading">Table of Contents</h1>
<div id="toc"></div>

```{.python .input  n=53}
%%javascript
$.getScript('https://kmahelona.github.io/ipython_notebook_goodies/ipython_notebook_toc.js')
```

```{.json .output n=53}
[
 {
  "data": {
   "application/javascript": "$.getScript('https://kmahelona.github.io/ipython_notebook_goodies/ipython_notebook_toc.js')",
   "text/plain": "<IPython.core.display.Javascript object>"
  },
  "metadata": {},
  "output_type": "display_data"
 }
]
```

# Introduction

In the 2015/16 NAEP process data scheme there is a compromise in the data
structure, where we needed to keep complex (hierarchical) information about the
state of objects in a single SQL string field. To the extend we can, we used
JSON to encode the data. The question is how to efficiently decode the data for
analysis.

# Loading JSON to dict

Here we compare two ways to convert the JSON string to a dict.

- JSON: http://stackoverflow.com/questions/19483351/converting-json-string-to-
dictionary-not-list-python
- AST: http://stackoverflow.com/questions/15197673/using-pythons-eval-vs-ast-
literal-eval

Here is one example keystroke datum. The eNAEP system somehow doubled the
double-quote in the JSON so we have to clear that first. We create 2 versions,
one with the original "true" and the other replacing it with "True", because the
JSON method requires `true` while the AST method requires `True`.

```{.python .input  n=2}
kstr = '{\
    ""name"":""text.change"",\
    ""textDiff"":{\
        ""diffs"":[{""edit"":""INS"",""pos"":1,""len"":1,""text"":""m""}],\
        ""textContext"":""m^ "",\
        ""textLength"":3},\
        ""selection"":{\
            ""selectedText"":"""",\
            ""startPos"":1,\
            ""endPos"":1,\
            ""lenSelected"":0,\
            ""textSelected"":"""",\
            ""isCollapsed"":true\
        }\
    }'

# we create 2 versions, one with the original "true" and the other replacing it with "True"
s1 = kstr.replace("\"\"", "\"")
print s1
s2 = kstr.replace("\"\"", "\"").replace("true", "True")
print s2

```

```{.json .output n=2}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "{    \"name\":\"text.change\",    \"textDiff\":{        \"diffs\":[{\"edit\":\"INS\",\"pos\":1,\"len\":1,\"text\":\"m\"}],        \"textContext\":\"m^ \",        \"textLength\":3},        \"selection\":{            \"selectedText\":\"\",            \"startPos\":1,            \"endPos\":1,            \"lenSelected\":0,            \"textSelected\":\"\",            \"isCollapsed\":true        }    }\n{    \"name\":\"text.change\",    \"textDiff\":{        \"diffs\":[{\"edit\":\"INS\",\"pos\":1,\"len\":1,\"text\":\"m\"}],        \"textContext\":\"m^ \",        \"textLength\":3},        \"selection\":{            \"selectedText\":\"\",            \"startPos\":1,            \"endPos\":1,            \"lenSelected\":0,            \"textSelected\":\"\",            \"isCollapsed\":True        }    }\n"
 }
]
```

## JSON

```{.python .input  n=3}
import json
j = json.loads(s1)
j["textDiff"]["textContext"]
```

```{.json .output n=3}
[
 {
  "data": {
   "text/plain": "u'm^ '"
  },
  "execution_count": 3,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

```{.python .input  n=3}
%timeit json.loads(s1)
```

```{.json .output n=3}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "100000 loops, best of 3: 11.8 \u00b5s per loop\n"
 }
]
```

## AST .literal_eval()

```{.python .input  n=4}
import ast
a=ast.literal_eval(s2)
a["textDiff"]["textContext"]
```

```{.json .output n=4}
[
 {
  "data": {
   "text/plain": "'m^ '"
  },
  "execution_count": 4,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

```{.python .input  n=5}
%timeit ast.literal_eval(s2)
```

```{.json .output n=5}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "10000 loops, best of 3: 60.8 \u00b5s per loop\n"
 }
]
```

## Conclusion

AST is about **6x** slower. JSON wins, and we don't need to convert `true` to
`True`.

Looking forward, here's probably how we are going to use it in Pandas:



```{.python .input  n=9}
import pandas as pd

ps = pd.Series([json.loads(s1), ast.literal_eval(s2)])
print ps
print "Accessing individual elements"
print ps.loc[0]["textDiff"]["textContext"]
print "\nGetting elements in as a Pandas Series"
print ps.apply(lambda x: x["textDiff"]["textContext"])
```

```{.json .output n=9}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "0    {u'selection': {u'isCollapsed': True, u'lenSel...\n1    {u'selection': {u'isCollapsed': True, u'lenSel...\ndtype: object\nAccessing individual elements\nm^ \n\nGetting elements in as a Pandas Series\n0    m^ \n1    m^ \ndtype: object\n"
 }
]
```

This also suggests that I should parse all `ExtendedInfo` entry into objects so
that I can access later.

# Retrieving info from a dict

Since we have nested data structure in JSON converted into a nested dict,
accessing it becomes a bit more complex. Here we compare the speed different
algorithms. It comes down to a trade off between **speed** and the
**expressiveness** of the method of data access.

- Native python: `a["textDiff"]["textContext"]`
- A path-like expression using `dpath`: `dpath.util.get(j,
'textDiff/textContext')`
- A XPath-like full-featured expression using `jsonpath_rw`:
`parse('textDiff.textContext').find(j)[0].value`



```{.python .input  n=47}
# install libraries
! pip install jsonpath-rw
! pip install dpath
from jsonpath_rw import jsonpath, parse
# [match.value for match in parse('textDiff.diffs.[*].len').find(j)]
import dpath.util

# for jsonpath_rw
def getVal(json, key):
    # make sure this is JSON
    # parse('textDiff.diffs.[*].len').find(j)[0].value
    try:
        res = parse(key).find(json)[0].value
    except:
        res = None
    return res
 
```

```{.json .output n=47}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "Requirement already satisfied (use --upgrade to upgrade): jsonpath-rw in /Users/garyfeng/anaconda/lib/python2.7/site-packages\nRequirement already satisfied (use --upgrade to upgrade): ply in /Users/garyfeng/anaconda/lib/python2.7/site-packages (from jsonpath-rw)\nRequirement already satisfied (use --upgrade to upgrade): decorator in /Users/garyfeng/anaconda/lib/python2.7/site-packages (from jsonpath-rw)\nRequirement already satisfied (use --upgrade to upgrade): six in /Users/garyfeng/anaconda/lib/python2.7/site-packages/six-1.10.0-py2.7.egg (from jsonpath-rw)\nCleaning up...\nRequirement already satisfied (use --upgrade to upgrade): dpath in /Users/garyfeng/anaconda/lib/python2.7/site-packages\nCleaning up...\n"
 }
]
```

## dict

First, after json parsing `j` is simple a dict. Let's time the good-o python
dict access. Result is typically in the 100 nano-seconds.

```{.python .input  n=48}
%timeit j["textDiff"]
```

```{.json .output n=48}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "The slowest run took 27.37 times longer than the fastest. This could mean that an intermediate result is being cached \n10000000 loops, best of 3: 113 ns per loop\n"
 }
]
```

## dpath
Next, let's use the `dpath` library. Typically in the 100 micro-seconds, or
**1,000x slower**.

```{.python .input  n=49}
%timeit dpath.util.get(j, 'textDiff')
```

```{.json .output n=49}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "The slowest run took 16.18 times longer than the fastest. This could mean that an intermediate result is being cached \n10000 loops, best of 3: 104 \u00b5s per loop\n"
 }
]
```

## jsonpath_rw
Using the full-featured `jsonpath_rw` is in the 6 ms range, or 60x slower than
`dpath`, or **60,000x slower than `dict`**.

```{.python .input  n=50}
%timeit parse('textDiff.textContext').find(j)[0].value
```

```{.json .output n=50}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "The slowest run took 8.60 times longer than the fastest. This could mean that an intermediate result is being cached \n100 loops, best of 3: 6.51 ms per loop\n"
 }
]
```

## Conclusion

Use the `dict` method as much as we can for performance, unless we really need
the flexibility of `dpath`. `jsonpath_rw` is nice but probably not necessary.


# Try notedown

Compile this iPynb into markdown, using `notedown`.


```{.python .input  n=54}
! pip install notedown
```

```{.json .output n=54}
[
 {
  "name": "stdout",
  "output_type": "stream",
  "text": "Downloading/unpacking notedown\n  Downloading notedown-1.5.0.tar.gz\n  Running setup.py (path:/private/var/folders/bg/4qvb4_0573v7j7j71bwk16dc0000gp/T/pip_build_garyfeng/notedown/setup.py) egg_info for package notedown\n    \nRequirement already satisfied (use --upgrade to upgrade): nbformat in /Users/garyfeng/anaconda/lib/python2.7/site-packages (from notedown)\nRequirement already satisfied (use --upgrade to upgrade): nbconvert in /Users/garyfeng/anaconda/lib/python2.7/site-packages (from notedown)\nRequirement already satisfied (use --upgrade to upgrade): ipython in /Users/garyfeng/anaconda/lib/python2.7/site-packages (from notedown)\nDownloading/unpacking pandoc-attributes (from notedown)\n  Downloading pandoc-attributes-0.1.7.tar.gz\n  Running setup.py (path:/private/var/folders/bg/4qvb4_0573v7j7j71bwk16dc0000gp/T/pip_build_garyfeng/pandoc-attributes/setup.py) egg_info for package pandoc-attributes\n    \nRequirement already satisfied (use --upgrade to upgrade): six in /Users/garyfeng/anaconda/lib/python2.7/site-packages/six-1.10.0-py2.7.egg (from notedown)\nDownloading/unpacking pandocfilters (from pandoc-attributes->notedown)\n  Downloading pandocfilters-1.3.0.tar.gz\n  Running setup.py (path:/private/var/folders/bg/4qvb4_0573v7j7j71bwk16dc0000gp/T/pip_build_garyfeng/pandocfilters/setup.py) egg_info for package pandocfilters\n    \nInstalling collected packages: notedown, pandoc-attributes, pandocfilters\n  Running setup.py install for notedown\n    \n    Installing notedown script to /Users/garyfeng/anaconda/bin\n  Running setup.py install for pandoc-attributes\n    \n  Running setup.py install for pandocfilters\n    \nSuccessfully installed notedown pandoc-attributes pandocfilters\nCleaning up...\n"
 }
]
```


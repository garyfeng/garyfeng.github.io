---
layout: post
title:  installing HDFStore
date: 2016-06-21 00:48:40
published: true
tags: [python, programming]
---

Trying to read and process eye-tracking data currently stored in the HDF5 format. Pandas supports [HDFStore](http://pandas.pydata.org/pandas-docs/stable/io.html#io-hdf5), see [also here](http://glowingpython.blogspot.com/2014/08/quick-hdf5-with-pandas.html). 

The problem is that somehow the python/anaconda installation on my mac is out of date or out of sync. It won't read the .hdf5 file, complaining the following:

```
>>> import pandas as pd
p>>> pd.HDFStore("test.hdf5")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/garyfeng/anaconda/lib/python2.7/site-packages/pandas/io/pytables.py", line 389, in __init__
    'importing'.format(ex=str(ex)))
ImportError: HDFStore requires PyTables, "dlopen(/Users/garyfeng/anaconda/lib/python2.7/site-packages/tables/utilsextension.so, 2): Library not loaded: @rpath/./libhdf5.10.dylib
  Referenced from: /Users/garyfeng/anaconda/lib/python2.7/site-packages/tables/utilsextension.so
  Reason: image not found" problem importing
>>> 

```

So I thought I'd reinstall PyTabls, using:

```
brew tap homebrew/science
brew install hdf5
```

This installed hdf5 version `1.8.16`, after which python complains that hdf5 version conflicts -- between `1.8.15_patch1` and the newly installed `1.8.16`. So I had to downgrade. 

Found the `hdf5.rb` old version at [https://raw.githubusercontent.com/Homebrew/homebrew-science/80f77e2ab89da3351c91af93fcf1f8c40b858628/hdf5.rb](https://raw.githubusercontent.com/Homebrew/homebrew-science/80f77e2ab89da3351c91af93fcf1f8c40b858628/hdf5.rb). Copy the content. 

Edit `/usr/local/Library/Taps/homebrew/homebrew-science/hdf5.rb`, which after `brew tap` is of version `1.8.16`. Paste in the above older version, and make the following changes at the very top:

```
class Hdf5 < Formula
  desc "File format designed to store large amounts of data"
  homepage "http://www.hdfgroup.org/HDF5"
  url "https://www.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8.5-patch1/src/hdf5-1.8.15-patch1.tar.bz2"
  version "1.8.15"
```

Save. Then back in terminal, do:

```
brew unlink hdf5
brew install hdf5
```

This now installs the older version `1.8.15`. It also warns that we don't have `sha256`. It reminds us the SHA256:

```
class Hdf5 < Formula
  desc "File format designed to store large amounts of data"
  homepage "http://www.hdfgroup.org/HDF5"
  url "https://www.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8.5-patch1/src/hdf5-1.8.15-patch1.tar.bz2"
  version "1.8.15"
  sha256 "1028be671e24dcd9826d3eabe6c0ebe674282368689dcf0f6bb5926bc8d3be25"
```

Done. However, `PyTables` still doesn't run. At least we have got HDF5 running. 

After this, I renamed the `hdf5.rb` to `hdf5.1.8.15.rb`, and restored the `v1.8.16` to `hdf5.rb`. 

---
layout: post
title: "Types coming to Python!"
date: 2015-1-3
comments: false
---

<p align='justify'>
If you ever made a list of things that makes high-level programming languages such as Python so slow, the list would be very, very long. And somewhere towards the top of that list would be dynamic types.
</p>

<p align='justify'>
When you declare a variable in C++, you specify its type. So the computer knows,  before the code is executed, things such as how large a memory space it will need to store it. This is not true in Python, where you can assign, at runtime, virtually anything to any variable. To enable this, Python variables have a lot of extra stuff wrapped to them that needs to be carried around. To see what this looks like, peek into object.h in your Python implementation (<b>/usr/include/python2.7/object.h</b> on my Linux laptop). While these dynamic types are really convenient, they also cause bad memory and cache locality (not to mention bloated memory use) which is really bad if you want to write fast code.
</p>

<p align='justify'>
There has never been a easy way to enforce static types in Python. (Indeed, being able to do static typing was one of the reasons compilers like <a href="http://docs.cython.org/src/quickstart/cythonize.html">Cython</a> have been so popular) Some projects like <a href="http://wphomes.soic.indiana.edu/jsiek/files/2014/03/retic-python.pdf">reticulated</a> and <a href="http://www.mypy-lang.org">mypy</a> have been around for some time, but never saw widespread use.
</p>

<p align='justify'>
<a href="https://quip.com/r69HA9GhGa7J">Well, it turns out that this might be about to change. According to Guido van Rossum, there is a proposal to add gradual typing to Python 3.5.</a>
</p>

<p align='justify'>
This certainly has the potential to turn Python syntax into a huge mess. However, if this is implemented well, it might actually convince me to abandon Python 2.7 in favor of Python 3. I never made the switch to Python 3, largely because nothing in Python 3, so far, made me particularly excited (at least not excited enough to sink the time to convert my existing codebase to Python 3). But this might actually be it.
</p>

<br>

<p align='justify'>
<b>Update:</b> (13 Feb 2015) Type hinting now has its own PEP: <a href='https://www.python.org/dev/peps/pep-0483/'>https://www.python.org/dev/peps/pep-0483/</a>
</p>

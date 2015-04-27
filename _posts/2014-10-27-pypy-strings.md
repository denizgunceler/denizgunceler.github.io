---
layout: post
title: "PyPy’s string woes"
date: 2014-10-27
comments: false
---

A similar issue has already been <a href="https://bitbucket.org/pypy/pypy/issue/926/">documented elsewhere on the internet</a>, 
but I don't think that the problem is solved. So I thought I might add mine to the mix.

I'm a huge fan of <em>compiled</em> python, i.e. things like <a href="http://cython.org/">Cython</a> and <a href="http://pypy.org/">PyPy</a>. 
<a href="http://dnzblg.wordpress.com/2014/10/20/how-bad-is-interpreter-overhead-in-python/">As I noted in a previous blog post</a>, 
they can be a huge performance boost. 
PyPy is my favourite, because you can get faster execution with no (or little) change to your source. 
However, if you happen to hit one of pypy's many quirks, it can be the cause of much frustration.

Unexpectedly, One of those quirks is string concatenation. 
I'll illustrate this with an example. When you start with an empty string and concatenate a bunch of random strings, 
you can quickly 'break' pypy. Take a look at the script below:

{% highlight python %}
import random
N = 100000
s = ''
for j in range(0,N):
    s += str(random.random())
{% endhighlight %}

Looks simple enough right? However, when I ran this script in pypy (<em>time pypy string_woes.py</em>), 
it took a whole 21.183 seconds to complete! 
The <emph>good old</emph> Python interpreter (<em>time python string_woes.py</em>) takes only 0.067 seconds. 
Clearly something is very messed up.

To make sure that the problem was indeed concatenation, 
I modified the code so that the random number would not be concatenated but rather written to a temp variable (and then discarded).

{% highlight python %}
import random
N = 100000
for j in range(0,N):
    tmp = str(random.random())
{% endhighlight %}

This time pypy takes 0.070 seconds, essentially the same time as the interpreted version.

I tried this using pypy 2.2.1 and later confirmed with the latest binary on the pypy website, 2.4.0 as of writing this. 
For python 3, again tested using pypy 2.4.0, the problem is much worse and the script takes over 3 minutes to run.

The problem, as explained in the link above, is that strings are immutable in PyPy.  
This means that every concatenation is probably allocating a new chuck of memory from scratch, resulting in O(n<sup>2</sup>) scaling, 
instead of the expected linear one.  
See the data/plot below for what I mean: (keep in mind that python runtimes are multiplied by 100 to help visibility)

<center>

<img class="aligncenter wp-image-107" src="{{ site.baseurl }}/images/pypy-strings/string_pypy.png" alt="string_pypy" width="85%"" />

<table border="0" cellspacing="0"><colgroup width="55"></colgroup> <colgroup width="73"></colgroup> <colgroup width="85"></colgroup>
<tbody>
<tr>
<th style="text-align: center;">N</td>
<th style="text-align: center;">Python (s)</td>
<th style="text-align: center;">PyPy (s)</td>
</tr>
<tr>
<td align="right" height="17">5000</td>
<td align="right">0.0023</td>
<td align="right">0.029</td>
</tr>
<tr>
<td align="right" height="17">10000</td>
<td align="right">0.0041</td>
<td align="right">0.1</td>
</tr>
<tr>
<td align="right" height="17">15000</td>
<td align="right">0.0067</td>
<td align="right">0.344</td>
</tr>
<tr>
<td align="right" height="17">20000</td>
<td align="right">0.0083</td>
<td align="right">0.715</td>
</tr>
<tr>
<td align="right" height="17">25000</td>
<td align="right">0.0102</td>
<td align="right">1.163</td>
</tr>
<tr>
<td align="right" height="17">30000</td>
<td align="right">0.013</td>
<td align="right">1.741</td>
</tr>
<tr>
<td align="right" height="17">35000</td>
<td align="right">0.0149</td>
<td align="right">2.406</td>
</tr>
<tr>
<td align="right" height="17">40000</td>
<td align="right">0.0183</td>
<td align="right">3.193</td>
</tr>
<tr>
<td align="right" height="17">45000</td>
<td align="right">0.0186</td>
<td align="right">4.069</td>
</tr>
<tr>
<td align="right" height="17">50000</td>
<td align="right">0.0214</td>
<td align="right">5.09</td>
</tr>
<tr>
<td align="right" height="17">55000</td>
<td align="right">0.0258</td>
<td align="right">6.23</td>
</tr>
<tr>
<td align="right" height="17">60000</td>
<td align="right">0.0256</td>
<td align="right">7.479</td>
</tr>
<tr>
<td align="right" height="17">65000</td>
<td align="right">0.0276</td>
<td align="right">8.893</td>
</tr>
<tr>
<td align="right" height="17">70000</td>
<td align="right">0.0291</td>
<td align="right">10.606</td>
</tr>
<tr>
<td align="right" height="17">75000</td>
<td align="right">0.0339</td>
<td align="right">12.091</td>
</tr>
<tr>
<td align="right" height="17">80000</td>
<td align="right">0.0349</td>
<td align="right">13.899</td>
</tr>
<tr>
<td align="right" height="17">85000</td>
<td align="right">0.0365</td>
<td align="right">15.851</td>
</tr>
<tr>
<td align="right" height="17">90000</td>
<td align="right">0.038</td>
<td align="right">17.83</td>
</tr>
<tr>
<td align="right" height="17">95000</td>
<td align="right">0.0399</td>
<td align="right">20.016</td>
</tr>
<tr>
<td align="right" height="17">100000</td>
<td align="right">0.0456</td>
<td align="right">22.41</td>
</tr>
<tr>
<td align="right" height="17">105000</td>
<td align="right">0.0474</td>
<td align="right">24.726</td>
</tr>
<tr>
<td align="right" height="17">110000</td>
<td align="right">0.0487</td>
<td align="right">27.368</td>
</tr>
<tr>
<td align="right" height="17">115000</td>
<td align="right">0.0503</td>
<td align="right">32.02</td>
</tr>
<tr>
<td align="right" height="17">120000</td>
<td align="right">0.0514</td>
<td align="right">34.907</td>
</tr>
</tbody>
</table>

</center>

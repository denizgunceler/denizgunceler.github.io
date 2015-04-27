---
layout: post
title: "How bad is interpreter overhead in Python?"
date: 2014-10-22
comments: false
---
<p style="text-align: justify;">
Everyone knows that Python is not the language of choice when writing high-performance code.  
The overhead due to the interpreter is too prohibitive 
unless you can express your problem entirely using libraries written in other languages 
(e.g. <a href="http://www.scipy.org/">scipy</a>, <a href="http://www.numpy.org/">numpy</a>), which might not be always possible.  
Despite this well-known fact, I wasn't able to find all that many benchmarks 
to see how much the python interpreter (CPython) overhead is, so I though I would do one myself.
</p>

<p style="text-align: justify;">
I wanted a simple but nontrivial example, so I choose the 
<a href="http://en.wikipedia.org/wiki/Primality_test" target="_blank">primality test code in Wikipedia</a>. 
I'm looking at how bad the interpreter overhead in CPython is and how the performance changes when the same algorithm is 'compiled'.  
I used Python 2.7.6, gcc 4.8.2, Cython 0.20.1 and PyPy 2.2.1.
</p>

<p style="text-align: justify;">
In my own work, I frequently use two Python 'compilers': 
<a href="http://cython.org">Cython</a> and <a href="http://pypy.org">PyPy</a>.  
Cython is a Python-to-C code translator that also allows for easy embedding of C code inside Python.  
PyPy on the other hand is a Python <a href="http://en.wikipedia.org/wiki/Just-in-time_compilation">just-in-time compiler</a>.  
I'll be comparing their performance to the commonly used CPython interpreter.  
Also, to see the 'best' performance I can get out of the same algorithm, I've implemented the same algorithm in C++ 
(I'm assuming you can't do much better than C++ without trying <em>really</em> hard).
</p>

<p style="text-align: justify;">
The source code (mostly from <a href="http://en.wikipedia.org/wiki/Primality_test" target="_blank">Wikipedia</a>) is below:
</p>


{% highlight python %}
def is_prime(num):
    if num <= 3:
        if num <= 1:
            return False
        return True
    if not num % 2 or not num % 3:
        return False
    for i in range(5, int(num ** 0.5) + 1, 6):
        if not num % i or not num % (i + 2):
            return False
    return True

def noPrimes( upper ):
    j = 2
    primes = 0
    while( j<upper ):
        if( is_prime(j) ):
            primes += 1
        j += 1
    return primes
{% endhighlight %}

<p style="text-align: justify;">
isPrime is a brute force algorithm that divides an input with (almost) all numbers that come before it and looks for a zero remainder. noPrimes counts the total number of primes up to the upper limit.  The total runtimes (in my laptop) for the above script for different upper limits are given (and plotted) below
</p>

<center>
<table style="text-align: center;" border="0" cellspacing="0" width=60% ><colgroup width="33"></colgroup> <colgroup width="63"></colgroup> <colgroup width="54"></colgroup> <colgroup width="45"></colgroup> <colgroup width="37"></colgroup>
<tbody>
<tr>
<th style="text-align: center;" align="center" height="17">N</td>
<th style="text-align: center;" colspan="4" align="left">time (seconds)</td>
</tr>
<tr>
<th align="left" height="17"></td>
<th style="text-align: center;" align="center">CPython</td>
<th style="text-align: center;" align="center">Cython</td>
<th style="text-align: center;" align="center">PyPy</td>
<th style="text-align: center;" align="center">C++</td>
</tr>
<tr>
<td align="center" height="17">1e6</td>
<td align="center">1.83</td>
<td align="center">1.29</td>
<td align="center">0.35</td>
<td align="center">0.13</td>
</tr>
<tr>
<td align="center" height="17">2e6</td>
<td align="center">4.72</td>
<td align="center">3.46</td>
<td align="center">0.8</td>
<td align="center">0.33</td>
</tr>
<tr>
<td align="center" height="17">3e6</td>
<td align="center">8.27</td>
<td align="center">6.25</td>
<td align="center">1.38</td>
<td align="center">0.58</td>
</tr>
<tr>
<td align="center" height="17">4e6</td>
<td align="center">12.21</td>
<td align="center">9.04</td>
<td align="center">2.04</td>
<td align="center">0.85</td>
</tr>
<tr>
<td align="center" height="17">5e6</td>
<td align="center">16.62</td>
<td align="center">12.27</td>
<td align="center">2.72</td>
<td align="center">1.17</td>
</tr>
<tr>
<td align="center" height="17">6e6</td>
<td align="center">21.35</td>
<td align="center">15.9</td>
<td align="center">3.49</td>
<td align="center">1.51</td>
</tr>
<tr>
<td align="center" height="17">7e6</td>
<td align="center">26.41</td>
<td align="center">19.8</td>
<td align="center">4.3</td>
<td align="center">1.87</td>
</tr>
<tr>
<td align="center" height="17">8e6</td>
<td align="center">31.81</td>
<td align="center">24.94</td>
<td align="center">5.17</td>
<td align="center">2.25</td>
</tr>
<tr>
<td align="center" height="17">9e6</td>
<td align="center">37.62</td>
<td align="center">28.83</td>
<td align="center">6.07</td>
<td align="center">2.65</td>
</tr>
<tr>
<td align="center" height="17">1e7</td>
<td align="center">43.40</td>
<td align="center">32.88</td>
<td align="center">7.03</td>
<td align="center">3.08</td>
</tr>
</tbody>
</table>
</center>

<center>
<img class="alignnone size-medium wp-image-163" src="{{ site.baseurl }}/images/python-overhead/lang.png" alt="lang" width="400"" />
<img class="alignnone size-medium wp-image-163" src="{{ site.baseurl }}/images/python-overhead/speedup.png" alt="lang" width="400"" />
</center>

<p style="text-align: justify;">
The first thing to note is that C++ is ~14 times faster than the CPython interpreter, so yes, the interpreter overhead is pretty bad in this example.  
I was pleasantly surprised by the performance of PyPy, which is almost half that of C++, a huge improvement over CPython.  
Cython on the other hand, barely makes a difference in this example, which was disappointing. 
In Cython's defence (I <em>really</em> like Cython), the script can be greatly improved by embedding C code; 
and using an unmodified python script does not necessarily reflect peak performance.
</p>

<p style="text-align: justify;">
This example does show how far PyPy has come, and that one day it might rival CPython as the most commonly used implementation of the Python language.  
Unfortunately, currently it has severe problems with C extensions, 
<a href="http://stackoverflow.com/questions/18946662/why-shouldnt-i-use-pypy-over-cpython-if-pypy-is-6-3-times-faster">which you can read about here</a>.  
But it has enormous potential if it overcomes all those issues.
</p>

---
layout: post
title: "The power of static types"
date: 2015-04-27
comments: false
---

[A while ago, I benchmarked the performance of the python interpreter using a simple primality test code]
({{site.baseurl}}{% post_url 2014-10-22-python-overhead %}),
noting that running the same code in [PyPy](http://pypy.org/) 
(a python [JIT](http://en.wikipedia.org/wiki/Just-in-time_compilation)) 
lead to huge performance gains
while translating the same code into C using [Cython](http://cython.org/) led to nowhere near the same performance.
This led us to speculate that in that particular example (testing numbers for primality)
it was not simply the interpreter overhead for loops, but something else, that was slowing down python.

As noted back then, this is not really Cython's fault. 
Compiling vanilla python code into C is not the optimal way of using Cython.
Its real power comes from the ability to easily embed C code, in particular statically typed variables.


My colleague Yalcin took [the code in my previous post]({{site.baseurl}}{% post_url 2014-10-22-python-overhead %}), 
and converted the most frequently used variables from dynamically typed to statically typed.
This means that at compile time, the computer knows about the type of the variable and can optimize accordingly.
Here is what that looks like:

{% highlight python %}
from libc.math cimport sqrt
cdef int is_prime(int num):
    if num <= 3:
        if num <= 1:
            return False
        return True
    if not num % 2 or not num % 3:
        return False
    cdef int i
    for i in range(5, <int>(sqrt(num))+1, 6):
        if not num % i or not num % (i + 2):
            return False
    return True
 
def noPrimes(int upper):
    cdef int j, primes
    j = 2
    primes = 0
    while( j<upper ):
        if( is_prime(j) ):
            primes += 1
        j += 1
    return primes
{% endhighlight %}

<br>

[If you compare this with the vanilla python code in my original post]({{site.baseurl}}{% post_url 2014-10-22-python-overhead %}),
you'll notice how little the code body has changed (mostly, a couple of **cdef**s were inserted).
However, for upper=5e6, the runtime (again on my modest laptop) drops spectacularly to **1.58** seconds 
from the almost **17** seconds it took for the python interpreter to do the job.
(For comparison, the vanilla python code, translated into C with Cython, takes about **12.27** seconds to complete!)
This is even faster than the **2.72** seconds it took for PyPy and approaches the **1.17** seconds it took for the C++ implementation.

Contemplate that for a second: A code written in python (highest of the high level languages) 
is approaching C++'s speed with only minor changes!

This is why I'm very excited about the initiative to bring type hinting to python. 
[You can read more about it here]({% post_url 2015-01-03-python-types %}).

---
layout: post
title: "Pisi Linux Review"
date: 2014-11-9
comments: false
---

<p align="center">
<img src="{{ site.baseurl }}/images/pisi-review/pisi-linux.png" alt="pisi-linix" />
</a>
</p>

A while ago, [I wrote]({{site.baseurl}}{% post_url 2014-10-22-pardus %}) (in Turkish) about the fact that <a href="http://en.wikipedia.org/wiki/Pardus_%28operating_system%29">Pardus Linux</a> distribution split into three new projects. I'm planning to try them all in due course, starting with Pisi. For the last month, I've been using <a href="http://en.pisilinux.org/">Pisi Linux</a> with much success and wanted to write a review on it. (<a href="http://www.distrowatch.com/pisi">also see Pisi at DistroWatch</a>)

Pisi Linux is a community supported distribution based on the <a href="http://en.wikipedia.org/wiki/Pardus_%28operating_system%29">original Pardus</a>, whereas the newer Pardus distributions are based on <a href="https://www.debian.org/">Debian</a>. Its main highlight is that it has it's own package manager (<a href="https://github.com/pisilinux/pisi">PiSi</a>) and it's own init system (<a href="https://github.com/pisilinux/mudur">mudur.py</a>); both of which (among other things) are written in Python. I was pleasantly surprised at how good the distribution is overall, and here are some of the things that make it so:

<ul>

	<li><span style="text-decoration: underline;"><strong>Rolling release model:</strong></span> Like <a href="https://www.archlinux.org/">Arch</a>, Pisi Linux is follows a rolling release model where everything (including the kernel) can be updated from the package manager. This is different from more popular distributions like Ubuntu which come in 'versions' (as of writing this, 14.04 is the latest LTS version). So with Pisi, you don't run into the problem of not being able to update software from the repositories because your version is not supported anymore.</li>
<br>
	<li><span style="text-decoration: underline;"><strong>Use the latest kernel:</strong></span> Another perk of following a rolling release model is that you end up using the latest available kernel (3.17 as of writing this). Linux has come a long (loooong) way in supporting all sorts of different hardware, but even so, it is not uncommon to encounter hardware that it has problems with.  I ran into such problems with my laptop more than once, so I can appreciate the ability to use the latest kernel. (You can check your kernel version by typing <em>uname -r</em> into a terminal) Also, you immediately get the performance benefits when <a href="http://lunarg.com/little-chicken-bit-story-big-performance-gain-intels-mesa-driver/">driver issues like this</a> come up.</li>
<br>
	<li><span style="text-decoration: underline;"><strong>Pisi package manager:</strong></span> Having used <em>apt</em> or <em>yum</em> since time immemorial, I wasn't exactly looking forward to a new package manager. But, I was very surprised at how quickly I got used to pisi and how well it performs.</li>
<br>
	<li><span style="text-decoration: underline;"><strong>Python:</strong></span> A lot of Pisi's core components are written in Python. This does not affect user experience all that much, but if you like looking under the hood to understand how things work (which I certainly do!), having components written in Python is very nice from a readability standpoint. Also, Python also makes it (much!) easier for new developers to come up to speed.</li>

</ul>

That said, Pisi does have some problems it needs to overcome.  The most obvious concerns not the operating system, but rather <a href="http://www.pisilinux.org/en">the website</a>.  The website currently appears to be in a confused state, which is especially true for the English version. If Pisi is to become a widely used distribution (it certainly has that potential and I hope it does), it needs a better organized website that  makes it easier for users.

---
layout: post
title: "USB 3.0 adventures with CyanogenMod, Android and Linux"
date: 2015-1-30
comments: false
---

<p align='justify'>
<a href="http://www.cyanogenmod.org">CyanogenMod</a> is an Android based open-source operating system for your phone or tablet.  
I own a Galaxy Tab 2 (p3110) device that I recently decided to flash with CyanogenMod.  
CyanogenMod is <a href="http://wiki.cyanogenmod.org">very well documented</a> these days, 
and Samsung does really make it easy for you to flash a new recovery with the Odin mode (kudos to Samsung for that).
</p>

<p align='justify'>
However, I did run into a nontrivial Android/Linux issue that I wanted to document it to spare others of the pain.
</p>

<p align='justify'>
Odin (or download) mode is a mode on most Samsung devices that allow you to flash new images on your phone or tablet.  
<a href="https://github.com/Benjamin-Dobell/Heimdall">Heimdall</a> is the open source implementation of the client side 
(i.e. your laptop) program that you use to interact with your phone/tablet when in Odin mode.  
The typical use of Heimdall is to override your stock recovery system with something more powerful 
(like <a href="http://wiki.cyanogenmod.org/w/ClockworkMod_Recovery#ClockworkMod_Recovery">ClockworkMod</a>) 
which you then use to flash <a href="http://www.cyanogenmod.org">CyanogenMod</a>, 
<a href="http://en.miui.com">MIUI</a> or some other custom ROM.
</p>

<p align='justify'>
I still don't know for sure what the heck this issue was all about, 
but I strongly suspect that it is a USB 3.0 driver issue involving the xhci_hcd driver on my Linux laptop (running 3.13 kernel).  
xhci_hcd is the Linux kernel module that handles USB 3.0 ports.  
It seems to be that the internet is full of people having issues trying to run 2.0 devices on 3.0 ports, 
and not just with Samsung devices. 
(see <a href="http://askubuntu.com/questions/457901/usb-2-0-device-scanner-does-not-work-with-xhci-hcd-on-usb-3-0-system">here</a> 
and <a href="http://askubuntu.com/questions/54555/samsung-galaxy-s2-in-download-mode-breaks-lsusb">here</a>)
</p>

<p align='justify'>
Unfortunately, xhci_hcd is unusual in the sense that you can't unload it with rmmod like most other kernel modules.  
Instead, you get the following error message:
</p>

<blockquote>rmmod: ERROR: Module xhci_hcd is builtin.</blockquote>

<p align='justify'>
So my problem went like this 
(see <a href="http://askubuntu.com/questions/54555/samsung-galaxy-s2-in-download-mode-breaks-lsusb">here</a> for a similar issue): 
I could connect my android device and use the debugging bridge 
(<a href="http://developer.android.com/tools/help/adb.html">adb</a>) fine when the device was booted normally, 
but the connection would fail if the device was in Odin mode.  
The problem wasn't with the open-source Heimdall client either, 
because lsusb would not detect the tablet at all.
</p>

<p align='justify'>
After much debugging I found out these facts:
</p>

- <p align='justify'>This issue only occurs when the USB port is a USB 3.0 port using xhci_hcd.  
My RaspberryPi and older laptop, both of which have only USB 2.0 ports using ehci_hcd module, work fine with the tablet.
</p>

- If you upgrade to a more recent Linux kernel (3.17 in my case), the problem goes away.  
[I use Pisi Linux]({% post_url 2014-11-9-pisi-review %}) which has a rolling release model, so upgrading to a new kernel was possible without re-installing my OS (in fact, it was trivially easy, since it can be done using the package manager!)

<p align='justify'>
So, based on these two facts, I am guessing that the problem was that the xhci_hcd driver in older (3.13) kernels was messed up.  
This is a major problem because the current long-term-support version of Ubuntu (14.04 LTS) uses the problematic driver in 3.13 
and USB 3.0 issues potentially affects a lot of people.  
Also kudos to Pisi and many other distros that embrace a development model with an 
<a href="http://en.wikipedia.org/wiki/Rolling_release">easily-upgradable kernel</a>.
</p>

<br>

<strong>Bonus:</strong>  Flashing CM 11 (Android 4.4 KitKat) instead of CM 10 (Android 4.2 Jelly Bean)

<p align='justify'>
To flash CM 11 to your Galaxy Tab, you need to be little careful and install a recent version of the ClockworkMod (6.0.4.4 or newer).  
If you simply go ahead and install the version on the <a href="http://wiki.cyanogenmod.org/w/Install_CM_for_p3110">CyanongenMod wiki</a>, 
you will be installing the 6.0.2.3 version, which will complain if you try flash CM 11 
(the <a href="http://download.cyanogenmod.org/?device=p3110&amp;type=stable">latest stable build</a>, which is CM10, works though). 
For CM 11, you want one of the more recent <a href="http://download.cyanogenmod.org/?device=p3110&amp;type=nightly">nightly builds</a> 
and the <a href="http://forum.xda-developers.com/showthread.php?t=2548257">most recent ClockworkMod</a> which you can find in the XDA forums.
</p>

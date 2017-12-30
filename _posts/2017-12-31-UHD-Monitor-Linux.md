---
tags: linux uhd 4k patch
layout: post
---

# Using my new UHD Monitor on Linux

## Intro

I got a new [Samsung UHD monitor](https://www.amazon.de/gp/product/B00WUACE4S/ref=as_li_tl?ie=UTF8&camp=1638&creative=6742&creativeASIN=B00WUACE4S&linkCode=as2&tag=euphi-21&linkId=c0fc1bd2a9f0d78b1514a87f39d73b07). However, my graphic chip, a several years old Radeon HD 6570 does not support the 4k resolution. At least, not officially.... But I found that it's quite simple to us. How simple? Well, it involves to apply a patch to the linux kernel sources and then to compile them.

With the patch, the linux graphic subsystem automatically detects the availbe UHD modes.
In my case, I was limited to set the refresh rate down to 24Hz, because 30Hz resulted in some sporadical problems.


## The patch

I found a good [article](http://www.elstel.org/software/hunt-for-4K-UHD-2160p.html.en) that describes the problem (maximum clock frequency of HDMI/DVI output) in detail, so please read that article if you are interested.

To summarize: The driver can be changed to allow higher frequency than the chipset supports offically.

The patch is linked at the end of that article - for convenience, I also prode the [link here](http://www.elstel.org/software/xorg.conf/0001-radeon.hdmimhz-parameter-introduced.patch).

The patch just allows you to set the maximum clock frequency with a kernel parameter named 'radeon.hdmimhz'.

Then, if the value is high enough, the linux graphic system automatically detects the proposed resolutions of your monitor (for UHD/4k (3840x2160) you need 275 or more. 297 is recommended for 30 Hz.)

### Applying the patch

I downloaded the latest kernel (in my case, the ubuntu kernel) and used 'make-kpkg' to build (see the [Ubuntu help](https://help.ubuntu.com/community/Kernel/Compile) on how to do this - or, if you are fluent in german, a better description at the [german ubuntu community](https://wiki.ubuntuusers.de/Kernel/Kompilierung/).

It seems the patch is too old to be applied automatically, so when you are using 'patch' and get error messages about _failed chunks_, edit the source files manually (the patch is very easy to understand, so you just need to add some variable declarations and small changes to existing functions).

## Building the kernel

I was using a self-compiled linux kernel for many years, but since some time I used the default kernel only. So I was quite suprised how long it takes to build a own kernel including all the modules - and how much hard disk space is needed.
So, I used the "make localmodconfig" feature. This generates a kernel configuration with only the modules that are currently needed. The build is much faster now.
To build I used 'make-kpkg', as explained in the german howto.

## First boot

To use the new feature, I changed '/etc/default/grub' to add the kernel parameter 'radeon.hdmimhz=297'.
After rebooting, the new resolution was available immediately. You can see the available modes with 'xrand' on command line.
The interesting line looks like this:
     3840x2160     30.00 +  25.00    24.00*   29.97    23.98  

As you see, the maximum refresh rate is limited to 30Hz. With some cards and good hdmi cable you may increase the maximum pixel clock to even more than 297. However, in my case, even 297Mhz seems to be a little bit to high. The refresh rate of 30 Hz works for me, but there are some distortions/black screens sometimes. So I lowered refresh rate to 24Hz.
For text/coding/browsing etc this is absolutely ok, but videos/games will suffer a little from this. For me this is acceptable.

## Graphic settings

I'm using KDE so I changed the screen resolution with the KDE system settings tool.

With a 4k resolution, the text fonts are a little bit small, so it is a good idea to override the font DPI and set it to a value like 120. Same applies for icons. Everything is found easily in the KDE system settings.
For chrome, I just increased the font size and everything is ok.

## Summary

Ok, you need to patch and compile your kernel - but this is not as complicated as it sounds. After this, everything works out of the box and you just need a little fine-tuning to make everything looking nice.



---
layout: post
title: Creating a bootable VHD the easy way…about Indexes
date: 2011-04-05 08:14:00.000000000 +02:00
tags:
  - Bootable VHD
  - Development Environment
  - Resolution
permalink: "/2011/04/05/creating-a-bootable-vhd-the-easy-wayabout-indexes/"
---

I’ve seen a lot of people using and downloading my bat-file from my original post [here]({{ site.baseurl }}{% link _posts/2010-09-23-creating-a-bootable-vhd-the-easy-way.md %}) and some of you have even tried some of my more advanced topics [here]({{ site.baseurl }}{% link _posts/2010-11-15-creating-a-bootable-vhd-the-easy-waycontinued.md %}).

There is something worth mentioning with the attached Install-WindowsImage.ps1 that I’ve received feedback from some of you out there. If you would like to install any other Operating System SKU then the default at Index 1 in my bat-file you’ll have to list the images first.

Let’s take a closer look at Install-WindowsImage.ps1:

![image]({{ site.baseurl }}/assets/images/2011/04/image_thumb.png)

As you can see someone has already been kind enough to provide us with an easy way to list the available images. So if I go ahead and run the example on my own system I get the following:

![image]({{ site.baseurl }}/assets/images/2011/04/image_thumb1.png)

So imagine if I wanted the DataCenter Edition (Full Installation) instead of Standard (Full Installation) I’d have to change the Index in the provided CreateBootableVHD_v2.bat file:

![image]({{ site.baseurl }}/assets/images/2011/04/image_thumb2.png)

Hope that clear any issues with Indexes you might have.

Enjoy!

Hugo

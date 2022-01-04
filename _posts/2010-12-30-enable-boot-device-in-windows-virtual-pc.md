---
title: Enable boot device in Windows Virtual PC
date: 2010-12-30 12:30:38.000000000 +01:00
categories:
  - Virtual Machines
tags:
  - Virtual Machines
permalink: "/2010/12/30/enable-boot-device-in-windows-virtual-pc/"
---

Well I finally got to the point where I start an old VM and I don’t remember the Administrators password for that machine although I installed it myself. I must be getting older or this specific machine was way old or my passwords are very very safe ![Blinkar]({{ site.baseurl }}/assets/images/2010/12/wlEmoticon-winkingsmile.png)

Anyway there are many ways to reset a users password and I used this [method](http://pogostick.net/~pnh/ntpasswd/bootdisk.html). But that meant that I had to boot from a DVD device inside my VM but I haven’t seen any BIOS setting ever in Windows Virtual PC. So this worked for me:

- Turn off Integration features
- Restart VM
- Hit the **DEL** button when your VM restarts and you’ll enter the BIOS settings

Cheers!

Hugo

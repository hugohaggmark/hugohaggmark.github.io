---
title: Creating a bootable VHD the easy way…continued
date: 2010-11-15 21:34:00.000000000 +01:00
tags:
  - Bootable VHD
  - Development Environment
  - SharePoint
permalink: "/2010/11/15/creating-a-bootable-vhd-the-easy-waycontinued/"
---

You might have read and tried the scripts from my first blog about creating a bootable vhd [in Creating a bootable vhd the easy way]({{ site.baseurl }}{% link _posts/2010-09-23-creating-a-bootable-vhd-the-easy-way.md %}), and in this blog post I’ll continue where I left with some information about my usage of difference discs and bootable vhd’s.

First of all I started by creating a bootable vhd with my script with the following options:

| Name          | Type       | Size                                                                                                                  |
| ------------- | ---------- | --------------------------------------------------------------------------------------------------------------------- |
| Base2k8r2.vhd | Expandable | 80 000 Mb (Remember that you need this much free space on your disc even if the vhd doesn’t have the size from start) |

This left me with a vhd-file that took about 7Gb on my internal disc. I restarted my machine to make sure that my bootable vhd was working correctly.

After the health-check and some customizations like folder options in explorer I opened up a command prompt and entered **_shutdown /r /f_** , this way I don’t have to enter a reason to restart my Windows Server 2008 R2 OS ![Blinkar]({{ site.baseurl }}/assets/images/2010/11/wlEmoticon-winkingsmile.png)

Back in **Windows 7** I took these steps to create a difference disc to keep my base disc clean:

1. Start a **_command prompt as Administrator_**
2. Write **_BCDEDIT /v_** and hit enter
3. You will see the boot entries and their GUIDs
4. Write **_BCDEDIT /delete {guid to the entry with a the path to your bootable vhd}_**
   > _**WARNING this will delete a boot entry, make sure you enter the GUID to the entry where the device/osdevice points the path of your bootable vhd. Entering the wrong guid could make your computer UNBOOTABLE.**_
5. Write **_DiskPart_** and hit enter
6. Enter _ **CREATE VDISK FILE="\<Path to difference vhd\>" PARENT="\<Path to parent vhd\>"** _  
   You must specify a valid folder name or you’ll get an error.
7. Write **_SELECT VDISK FILE=”\<Path to difference vhd\>”_** and hit enter
8. Write **_ATTACH VDISK_** and hit enter
9. Write **_Exit_** and hit enter
10. Write **_BCDBOOT \<your assigned drive letter\>:\Windows_** and hit enter
11. If you write **_BCDEDIT /v_** and hit enter you should see a new boot entry that points to your new difference disc.
12. Restart your machine and make sure that your bootable difference disc starts correctly.

When I get some time I’ll try to automate these steps into a handy batch-file or application. I hope you found this post interesting.

Cheers!

Hugo

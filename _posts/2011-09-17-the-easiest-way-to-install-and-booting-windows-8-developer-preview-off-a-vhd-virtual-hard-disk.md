---
layout: post
title: The easiest way to Install and Boot Windows 8 Developer Preview off a VHD (Virtual
  Hard Disk)
date: 2011-09-17 16:28:00.000000000 +02:00
categories:
  - Bootable VHD
  - Development Environment
  - Windows 8
tags:
  - Bootable VHD
  - Development Environment
  - Windows 8
permalink: "/2011/09/17/the-easiest-way-to-install-and-booting-windows-8-developer-preview-off-a-vhd-virtual-hard-disk/"
---

[![works-on-my-machine-starburst_3]({{ site.baseurl }}/assets/images/2011/09/works-on-my-machine-starburst_3_thumb.png "works-on-my-machine-starburst_3")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/works-on-my-machine-starburst_3.png)

This post provides the fastest way to get your Windows 8 Developer Preview booting off a VHD. If you’re interested in more details read my previous posts [here](http://www.hugohaggmark.com/2010/09/23/creating-a-bootable-vhd-the-easy-way/ "creating-a-bootable-vhd-the-easy-way"), [here](http://www.hugohaggmark.com/2010/11/15/creating-a-bootable-vhd-the-easy-waycontinued/ "creating-a-bootable-vhd-the-easy-waycontinued") and [here](http://www.hugohaggmark.com/2011/04/05/creating-a-bootable-vhd-the-easy-wayabout-indexes/ "creating-a-bootable-vhd-the-easy-wayabout-indexes").

1. First of all download Windows 8 Developer Preview [here](http://msdn.microsoft.com/en-us/windows/apps/br229516 "Windows 8 Developer Preview").
2. Download my script [here](http://www.hugohaggmark.com/wp-content/uploads/2010/09/CreateBootableVHD_v22.zip "CreateBootableVHD_v22.zip").
3. Select Properties on the zip-package and click on the **Unblock** button.
4. Extract the zip-package to a folder of your choice.
5. Mount Windows 8 Developer Preview with your favorite mounting tool, my favorite tool is Virtual Clone Drive you can find it [here](http://static.slysoft.com/SetupVirtualCloneDrive.exe "VirtualCloneDrive.exe").
6. Look for the file “install.wim” in your installation media and remember the path, in my case F:\sources\install.wim.
7. Open a **PowerShell** command prompt with the **run as Administrator** option. You need to run the attached bat-file from a **PowerShell** console as an **Administrator** and not from the usual command prompt.
8. Change directory to the directory where you extracted the zip-package.
9. Before I show you how to use the CreateBootableVHD.bat file there is 2 important concepts that you need to know:

- The third parameter in CreateBootableVHD.bat expects you to enter the type of VHD that you want to create. If you choose **EXPANDABLE** you will need to make sure that you have the specified amount of GB left for the VHD even though the VHD will be small at first.
- CreateBootableVHD.bat will assign your bootable VHD a drive letter, it’s very important to use a **FREE drive letter**.
- When you enter a drive letter make sure you don’t enter a colon after the letter i.e. **X** and never **X:**.

10. Now you’re ready to use the CreateBootableVHD.bat-file like this:  
    **CreateBootableVHD_v2.bat  
    \<path where you like to store the VHD, doesn’t work on external drives\>  
    \<size in MB\>  
    \<type FIXED|EXPANDABLE\>  
    \<drive letter assigned to the VHD\>  
    \<path to the wim-file, see number 6 above\>**  
    _ **Ex:** _ _ **.\CreateBootableVHD_v2.bat C:\VHD\Win8.vhd 80000 FIXED X F:\sources\install.wim.** _

Sit back and enjoy as the complete automated process of getting your copy of Windows 8 Developer Preview bootable.

Hope you enjoy it!

Hugo

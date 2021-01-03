---
layout: post
title: Generating BizTalk binding files with Excel automation in a TFS Build
date: 2011-04-13 17:25:43.000000000 +02:00
tags:
  - BizTalk
  - Error
  - Resolution
  - Team Build
permalink: "/2011/04/13/generating-biztalk-binding-files-with-excel-automation-in-a-tfs-build/"
---

This is yet another of those things I really need to blog about so that I don’t ever forget this personally

### Background

So the background is as follows:

- A large BizTalk 2010 implementation at a large private Swedish Enterprise customer.
- [Visual Studio](http://www.microsoft.com/visualstudio/en-us) 2010 and TFS 2008
- Developers have already been working more or less 1 year before I arrive at the scene.
- My mission is to automate the whole build/release process.
- The devs have written an Excel macro that takes values from the Excel WorkSheet and together with a template-file for BizTalk bindings it generates all the different binding-files for the specified environments in the Excel Worksheet. I’ve seen a lot’s of different ways to do this before and this is yet another.
- The Excel macro is called from a vbs-file.
- The vbs-file is called from a powershell script, yes I see a lot of improvement here.

### The challenge

So I started my journey today with the hopes of just hooking up the existing scripts for generating the BizTalk binding-files with a [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) build. But for some reason no files were generated but I could see that Excel started and so forth.

After I while I decided to leave the Excel macro thingee and rewrite the complete Excel macro in C#, and I did and it worked just fine.

I hooked up my little console app with my build and got the following message:

_**System.Runtime.InteropServices.COMException (0x800A03EC): Microsoft Office Excel cannot access the file 'c:\temp\test.xls'. There are several possible reasons:**_

- The file name or path does not exist.
- The file is being used by another program.
- The workbook you are trying to save has the same name as a currently open workbook.

Finally! some error I could search for.

### The solution

I found the solution [here in Excel 2007 automation on top of a Windows Server 2008 x64](http://social.msdn.microsoft.com/Forums/en/innovateonoffice/thread/b81a3c4e-62db-488b-af06-44421818ef91) and it stipulates that you should add the following folder structure to the server where you’re running Excel automation:

- Windows 2008 Server x64  
   Please create this folder: **C:\Windows\SysWOW64\config\systemprofile\Desktop**
- Windows 2008 Server x86  
   Please create this folder: **C:\Windows\System32\config\systemprofile\Desktop**

It almost felt silly after some 4 hours of rewriting an Excel macro to C# to just add a the _**Desktop**_ folder under _**C:\Windows\SysWOW64\config\systemprofile**_ and the build just worked perfectly!

Warning read this official Microsoft kb about support for this [here in Considerations for server-side Automation of Office](http://support.microsoft.com/kb/257757).

### Lessons learned

- VBA code can be very messy
- You should start to think about automated build processes from day one in a dev project.
- I never thought adding a folder could solve this

Cheers!

Hugo

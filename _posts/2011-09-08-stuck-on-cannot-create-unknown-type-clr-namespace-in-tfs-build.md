---
layout: post
title: Stuck on “Cannot create unknown type {clr-namespace:” in TFS Build?
date: 2011-09-08 15:51:00.000000000 +02:00
tags:
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
permalink: "/2011/09/08/stuck-on-cannot-create-unknown-type-clr-namespace-in-tfs-build/"
---

## Background

A couple of days ago I was upgrading some of my clients [MSBuild tasks](http://msdn.microsoft.com/en-us/library/z7f65y0d.aspx) to WorkFlow Activities following the _**Rewrite MSBuild task as a Workflow Activity**_ option in the guidelines from this excellent [post "Upgrade Paths for Custom MSBuild Tasks"](http://blogs.msdn.com/b/willbar/archive/2009/11/12/upgrade-paths-for-custom-msbuild-tasks.aspx) by [William Bartholomew](http://blogs.msdn.com/b/willbar/).

I stumbled upon a custom Assembly Versioning MSBuild task and I took a moment of thought to see what I could do to improve this MSBuild task. I read this great [post "Versioning Code in TFS – Revised"](http://freetodev.wordpress.com/2009/11/07/versioning-code-in-tfs-revised/) by Mike Fourie's and yet another great [post "TFS 2010 Build Number and Assembly File Versions: Completely In Sync with Only MSBuild 4.0"](http://www.wintellect.com/CS/blogs/jrobbins/archive/2009/11/09/tfs-2010-build-number-and-assembly-file-versions-completely-in-sync-with-only-msbuild-4-0.aspx) by John Robbins that I really encourage anyone doing custom versioning in [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) 2010 to read.

I decided to follow some of the posts recommendations and decided that for the purposes of my client I would not need to convert this (former) MSBuild task into a coded [CodeActivity](http://msdn.microsoft.com/en-us/library/system.workflow.activities.codeactivity.aspx) because a designed activity would be more then enough.

Anyway a simplified version of the clients workflow activity looked something like this:  
![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb.png)

With some input arguments that looked like this:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb1.png)

Everything is pretty straightforward if you’re custom to TFS Build and [Windows Workflow Foundation](http://msdn.microsoft.com/en-us/netframework/aa663328) except for the Activites in the Then/Else sections. In the Then/Else sections I’m using the AssemblyInfo Activity from [Community TFS Build Extension.](http://tfsbuildextensions.codeplex.com/)

## Challenge

So I check-in my custom assembly and make sure that the controller is pointing to the folder in the source control tree that contains my custom assemblies.  
![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb2.png)

I then added my new Activity to the build process template and ran my first build against that build process template…_**BOOOOOM!**_ Got this message:

```
TF215097: An error occurred while initializing a build for build definition \Tailspin Toys\HugoHaggmark: Cannot create unknown type '{clr-namespace:HugoHaggmark.TfsBuild.Activities}Example'.
```

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb3.png)

## Solution

Are you in the same situation as me? Don’t despair a simple solution is close. ![Ler]({{ site.baseurl }}/assets/images/2011/09/wlEmoticon-smile.png)

1. Firstly open up build process template as XML.
2. Find the reference to your Custom Activity in the XML (probably in the top section of the XML), looks something like this:

   ```xml
   xmlns:local="clr-namespace:<YourNameSpace>"
   ```

   or in my case I had this:

   ```xml
   xmlns:local="clr-namespace:HugoHaggmark.TfsBuild.Activities"
   ```

3. Add ;assembly=<Name of your assembly> to that reference so in my case the end result looked like this:

   ```xml
   xmlns:local="clr-namespace:HugoHaggmark.TfsBuild.Activities;assembly=HugoHaggmark.TfsBuild.Activities"
   ```

4. Create a partial class right next to your Custom Activity and add the BuildActivity attribute like shown below:  
   ![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb4.png)
5. Check In, rebuild and redeploy assemblies to the custom assemblies folder.
6. Run the build and voila! The build error is gone!

Cheers,

Hugo

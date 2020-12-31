---
layout: post
title: Moles issue when multiple projects reference the same moles assembly and the
  assembly version changes
date: 2011-12-23 11:15:00.000000000 +01:00
categories:
  - Error
  - Moles
  - Resolution
  - Team Build
tags:
  - Error
  - Moles
  - Resolution
  - Team Build
permalink: "/2011/12/23/moles-issue-when-multiple-projects-reference-the-same-moles-assembly-and-the-assembly-version-changes/"
---

## Background

The other day I had one of the teams at my current customer report a strange build error in [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") build. They could tell that it had something to do with [Moles](http://research.microsoft.com/en-us/projects/pex/ "Pex and Moles - Isolation and White box Unit Testing for .NET") but they couldn’t solve it.

The solution they were trying to build was pretty simple and consisted of on Class Library project and two Test projects, one for unit testing and the second for integration testing. It looked something like the picture below:  
[![image]({{ site.baseurl }}/assets/images/2011/12/image_thumb.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/12/image.png)

## Challenge

I noticed right away that both test projects had moles references to the library which doesn’t have to be a bad thing. But then I also noticed that one of the test projects had a reference to the other test project as well. Could this result in a conflict resolving the correct Moles assembly?

Well if you DO NOT change the assembly version of the library project then you WILL NOT have a problem. But if you’re like us and like to sync your AssemblyVersion and AssemblyFileVersion with the build in TFS then you will run into this trouble. You’ll probably get an error message like:

> **The type or namespace name 'Moles' does not exist in the namespace (are you missing an assembly reference?)**

Mind you that this error message is a common compile error in Moles but does also appear in this case. I’ve attached a small solution that mimics this behavior [here](http://www.hugohaggmark.com/wp-content/uploads/2011/12/HugoHaggmark.Blog_.MolesIssue.zip "solution containing moles issue in this post"). (you will need VS2010 and Moles installed to build the zipped solution)

## Solution

You get this error because when you add a Moles reference in your test project it adds a reference to a specific version of the Moled assembly (this is of course the default behavior you want in most cases) but when two assemblies have the same Mole reference and they reference each other we get a copy conflict.

To solve this I’ve come up with these solutions:

1. First of all you should probably break the project reference between the two test projects and then your problems will automatically go away.
2. When you add a Moles Reference from within [Visual Studio](http://www.microsoft.com/visualstudio/en-us "Visual Studio") you’ll get a reference to a specific version like so:  
   \<Reference&nbsp;Include="Type.Moles, Version=1.0.0.0, Culture=neutral  
   , processorArchitecture=MSIL" /\>  
   If you change the property Specific Version on the Moles reference to False then that will solve this issue as well.  
   [![image]({{ site.baseurl }}/assets/images/2011/12/image_thumb1.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/12/image1.png)

I wish that the command “Upgrade .Moles files..” on the solution level would automatically update the project references with the new versions of the Mole References. But I believe I have to file that request to [Microsoft](http://www.microsoft.com/en-us/default.aspx "Microsoft").  
[![image]({{ site.baseurl }}/assets/images/2011/12/image_thumb2.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/12/image2.png)

Enjoy,

Hugo

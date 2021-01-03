---
layout: post
title: Building and Unit Testing .Net 4.0 with Team Build 2008, the correct order
date: 2011-03-03 10:44:00.000000000 +01:00
tags:
  - ".Net 4.0"
  - Environment
  - Recommended Practices
  - Resolution
  - Team Build
  - Team Foundation Server
permalink: "/2011/03/03/building-and-unit-testing-net-4-0-with-team-build-2008-the-correct-order/"
---

I’ve been helping my customer setting up .Net 4.0 build servers lately as you may have read in my previous [post]({{ site.baseurl }}{% link _posts/2010-11-24-building-and-unit-testing-net-4-0-with-team-build-2008.md %} "building-and-unit-testing-net-4-0-with-team-build-2008"). In the previous post, I listed my challenges getting .Net 4 assemblies to build and unit test with Team Build 2008 so I thought It would be time to share the correct order to get the stuff working!

1. Install Visual Studio 2010 Premium
2. Install Visual Studio Team System 2008 Development Edition
3. Install Visual Studio Team System 2008 Service Pack 1
4. Install Team Build 2008
5. Install [Team Build Service Pack 1](http://www.microsoft.com/downloads/details.aspx?FamilyId=9E40A5B6-DA41-43A2-A06D-3CEE196BFE3D&displaylang=en "Team Build Service Pack 1")
6. Configured Team Build 2008 to use MSBuild 4.0 instead of MSBuild 3.5. To do this edit %ProgramFiles%\Microsoft Visual Studio 9.0\Common7\IDE\PrivateAssemblies\TFSBuildService.exe.config and set the [MSBuildPath property](http://msdn.microsoft.com/en-us/library/aa833875.aspx) to C:\Windows\Microsoft.NET\Framework\v4.0.21006\

That should do it! And if you install your build server in that order you won’t get the following error:

- _ **MSBUILD : warning : Visual Studio Team System for Software Testers or Visual Studio Team System for Software Developers is required to run tests as part of a Team Build** _
- **_And you won’t replace the Microsoft.TeamFoundation.Build.targets with an incorrect workspace pattern:_  
  TFS Build 2008:  
  $(COMPUTERNAME)\_$(BuildDefinitionId)  
  After VS 2010 installation:  
  $(COMPUTERNAME)\_$(BuildDefinitionId)\_$(BuildAgentId)**

Hope this summarizes how to install a build server that builds and unit tests .Net 4.0 applications with Team Build 2008.

Cheers!

Hugo

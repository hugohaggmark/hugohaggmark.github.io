---
layout: post
title: Building and Unit Testing .Net 4.0 with Team Build 2008
date: 2010-11-24 19:56:00.000000000 +01:00
tags:
  - ".Net 4.0"
  - BizTalk
  - Environment
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
  - Unit Testing
permalink: "/2010/11/24/building-and-unit-testing-net-4-0-with-team-build-2008/"
---

One of the tasks this week was to install and configure a new BizTalk 2010 build server in my clients environment. Pretty straight forward you may say huh? Well it would be if my client was using TFS 2010 but in this case we’re talking about a TFS 2008.

There are lots of posts like this great [post "Building .NET 4.0 Applications Using Team Build 2008"](http://blogs.msdn.com/b/willbar/archive/2009/11/01/building-net-4-0-applications-using-team-build-2008.aspx) explaining how to do this and the building part worked fine. But I kept getting error telling me that unit tests where not able to run because I didn’t have the correct version of Visual Studio installed. Anyway here are the steps I followed to succeed with this endeavor:

- Install BizTalk 2010 compilation components
- Installed Team Build 2008
- Installed [Team Build Service Pack 1](http://www.microsoft.com/downloads/details.aspx?FamilyId=9E40A5B6-DA41-43A2-A06D-3CEE196BFE3D&displaylang=en)
- Configured Team Build 2008 to use MSBuild 4.0 instead of MSBuild 3.5. To do this edit _**%ProgramFiles%\Microsoft Visual Studio 9.0\Common7\IDE\PrivateAssemblies\TFSBuildService.exe.config**_ and set the [MSBuildPath property](http://msdn.microsoft.com/en-us/library/aa833875.aspx) to _**C:\Windows\Microsoft.NET\Framework\v4.0.21006**_
- Restarted the Team Foundation Build service
- Tried a simple Hello World application without unit tests and the build worked fine.
- Added some unit tests to the Hello World application and got error the following error _**MSBUILD : warning : Visual Studio Team System for Software Testers or Visual Studio Team System for Software Developers is required to run tests as part of a Team Build**_
- Installed Visual Studio 2010 Premium
- Got new error telling me that there was some problem with workspace creation during build, found this [solution "Visual Studio 2010 broke TFS Build 2008 (WorkspaceName"](http://connect.microsoft.com/VisualStudio/feedback/details/534227/visual-studio-2010-broke-tfs-build-2008-workspacename).
- Changed the property _**$(COMPUTERNAME)\_$(BuildDefinitionId)\_$(BuildAgentId)**_ to _**$(COMPUTERNAME)\_$(BuildDefinitionId)**_ in the file _**%ProgramFiles%\Microsoft Visual Studio 9.0\Common7\IDE\PrivateAssemblies\TFSBuildService.exe.config**_.
- Got back the same old error _**MSBUILD : warning : Visual Studio Team System for Software Testers or Visual Studio Team System for Software Developers is required to run tests as part of a Team Build**_
- Installed Visual Studio Team System 2008 Development Edition
- Finally the build worked fine and all the unit tests ran successfully!

Hope this will summarize some of the issues you might run into when trying to build and unit test .Net 4.0 applications with Team Build 2008.

Cheers!

Hugo

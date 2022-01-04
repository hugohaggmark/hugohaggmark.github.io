---
title: End to end TFSBuild pattern for BizTalk 2009, part 2 (Tools)
date: 2010-09-28 07:00:44.000000000 +02:00
tags:
  - BizTalk
  - Development Environment
  - Team Build
  - Tools
permalink: "/2010/09/28/end-to-end-tfsbuild-pattern-for-biztalk-2009-part-2-tools/"
---

Ok, you finally got your build environment installed and [tested]({{ site.baseurl }}{% link _posts/2010-09-26-end-to-end-tfsbuild-pattern-for-biztalk-2009-part-i-environment.md %}) with a simple Hello World build and you're ready for the next phase.

Before I show you the good stuff J there is some tools that you need to succeed with this quest:

- You'll need basic knowledge of [MSBuild](<http://msdn.microsoft.com/en-us/library/0k6kkbsd(v=VS.90).aspx>) and [TFSBuild](<http://msdn.microsoft.com/en-us/library/ms400688(v=VS.90).aspx>) or you could copy and paste my build pattern if you like J
- I mainly use the OOB tools that come with .Net framework and BizTalk i.e. [BTSTask](<http://msdn.microsoft.com/en-us/library/aa559686(BTS.10).aspx>), [BTSWCFServicePublishing](<http://msdn.microsoft.com/en-us/library/bb245933(BTS.20).aspx> and [GacUtil](<http://msdn.microsoft.com/en-us/library/ex0ss12c(v=VS.90).aspx>) with my builds because I find them to be stable, supported and generally well documented.  
  _These tools should already be installed in your environment but you'll have to make sure that you have the correct [permissions]({{ site.baseurl }}{% link _posts/2010-09-26-end-to-end-tfsbuild-pattern-for-biztalk-2009-part-i-environment.md %}) on your TFSBuild service account to use them._
- I use [BizTalk MsBuild Generator](http://biztalkmsbuildtool.codeplex.com/releases/view/31725) to start and stop BizTalk applications so you will need to download the tool and extract **BizTalk.BuildGenerator.Tasks.dll** and **BizTalk.BuildGenerator.tasks**. These two files are then placed under source control with the teambuildtype.
- If you need automatic generation of the BizTalk binding-files from a template (for different target environment for instance) I have found the [XMLPreprocess](http://xmlpreprocess.codeplex.com/) tool very helpful.
- In the early years I used the [SDC tasks](http://sdctasks.codeplex.com/) but it's no longer supported but there is still a lot of good stuff in that package if you want to learn [MSBuild](<http://msdn.microsoft.com/en-us/library/0k6kkbsd(v=VS.90).aspx>) in more depth. The replacement is called [MSBuild Extension Pack](http://www.msbuildextensionpack.com/) if you want to check it out. While you're at it you should really checkout [MSBuild Explorer](http://www.msbuildexplorer.com/).

Try to install the tools in a common directory i.e. _**C:\BuildTools**_ or something similar and keep these tools under source control at all time.

Until next time have a good one!

Hugo

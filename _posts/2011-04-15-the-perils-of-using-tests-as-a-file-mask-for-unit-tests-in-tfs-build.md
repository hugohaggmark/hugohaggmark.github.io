---
title: The perils of using “.Tests” as a file mask for unit tests in TFS Build
date: 2011-04-15 07:47:00.000000000 +02:00
tags:
  - BizTalk
  - Error
  - Resolution
  - Team Build
  - Unit Testing
permalink: "/2011/04/15/the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build/"
---

### Background

As some of you might already have read [Generating BizTalk binding files with Excel automation in a TFS Build]({{ site.baseurl }}{% link _posts/2011-04-13-generating-biztalk-binding-files-with-excel-automation-in-a-tfs-build.md %}) I’m currently working with a large BizTalk implementation that has been going on for more or less a year.

So there’s already tons of unit tests in place and one of the first things I’d hade to do is setup some [CI-builds](http://en.wikipedia.org/wiki/Continuous_integration) to verify that each check-in keeps the integrity of the source control tree intact and run unit tests.

### The Challenge

The hardest challenge when I start coaching a team with devs with no automated builds this late in the game is:

- Talking about the value that automation brings to their day to day development work
- Introducing automation without changing the already established routines, i.e. keeping balance between the new and established routines

Here is a team that already established a routine for creating unit test projects for instance where they name all their unit test projects _**[Customer].[BTSSystem].[BTSArtifact].Tests**_. Not going to change this routine right? I’m going to use it in my CI-builds instead right?

I create a CI-build (read more [here]({{ site.baseurl }}{% link _posts/2011-04-15-build-recipe-for-running-unit-tests-using-file-masks-in-tfs-build-2008.md %}) that looks for the file mask .Tests (we’re using [TFS 2008](http://msdn.microsoft.com/en-us/vstudio/ff637362)) and off we go!

### The Error

Some days later I check back in (I’m not at the customer every day, otherwise I would constantly monitor the builds) to follow up how the builds are going and I found one very odd build that was constantly _**"partially succeeded"**_. Learning from my earlier mistakes a [read my blog post about “partially succeeded” builds]({{ site.baseurl }}{% link _posts/2010-12-08-build-partially-succeeded-but-hey-no-obvious-errors.md %}) and I found nothing that could help me identify the error.

Our build log looked like this:

```log
Using "TestToolsTask" task from assembly "E:\Program Files (x86)\Microsoft Visual Studio 90\Common7\IDE\PrivateAssemblies\Microsoft.VisualStudio.QualityTools.MSBuildTasks.dll".
Task "TestToolsTask"
Command:
E:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\MSTest.exe /nologo
/runconfig:"E:\Blds\73\Sources\Dev\[Customer].Dev.CI.[BtsApp].testsettings"
/searchpathroot:"E:\Blds\73\Binaries\Debug"
/resultsfileroot:"E:\Blds\73\TestResults"
/testcontainer:"E:\Blds\73\Binaries\Debug\[Customer].[BtsApp].Maps.Tests.dll"
/testcontainer:"E:\Blds\73\Binaries\Debug\[Customer].[BtsApp].Orchestration.Tests.dll"
/publish:"http://sometfs:8080/"
/publishbuild:"vstfs:///Build/Build/6829"
/teamproject:"[Customer].
/platform:"Any CPU"
/flavor:"Debug"
The "TestToolsTask" task is using "MSTest.exe" from "E:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\MSTest.exe".
Loading E:\Blds\73\Sources\Dev\[Customer].Dev.CI.[BtsApp].testsettings...
Loading E:\Blds\73\Binaries\Debug\[Customer].[BtsApp].Maps.Tests.dll...
Loading E:\Blds\73\Binaries\Debug\[Customer].[BtsApp].Orchestration.Tests.dll...
Starting execution...
TESTTOOLSTASK : warning : The disabled test 'SomeTest' was removed from the test run. [E:\Blds\73\BuildType\TFSBuild.proj]
Results               Top Level Tests
-------               ---------------
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest1
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest2
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest3
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest4
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest5
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest6
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest7
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest8
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest9
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest10
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest11
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest12
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest13
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest14
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest15
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest16
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest17
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest18
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest19
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest20
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest21
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest22
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest23
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest24
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest25
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest26
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest27
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest28
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest29
Passed                [Customer].[BtsApp].Maps.Tests.SomeTest30
Passed                [Customer].[BtsApp].Orchestration.Tests.SomeTest1
Passed                [Customer].[BtsApp].Orchestration.Tests.SomeTest2
Passed                [Customer].[BtsApp].Orchestration.Tests.SomeTest3
Passed                [Customer].[BtsApp].Orchestration.Tests.SomeTest4
34/34 test(s) Passed
Summary
-------
Test Run Warning.
Passed  34

---

Total   34
Results file:  E:\Blds\73\TestResults\SOME-TFS 2011-04-01 15_29_30_Any CPU_Debug.trx
Test Settings: Local

Run has the following issue(s):
Waiting to publish...
Publishing results of test run SOME-TFS 2011-04-01 15:29:30_Any CPU_Debug to http://sometfs:8080/...
..Publish completed successfully.
Command:
C:\Program Files (x86)\Microsoft Visual Studio 9.0\Common7\IDE\MSTest.exe /nologo
/runconfig:"E:\Blds\73\Sources\Dev\[Customer].Dev.CI.[BtsApp].testsettings"
/searchpathroot:"E:\Blds\73\Binaries\Debug"
/resultsfileroot:"E:\Blds\73\TestResults"
/testcontainer:"E:\Blds\73\Binaries\Debug\[Customer].[BtsApp].OrchestrationsHelper.Tests.dll"
/publish:"http://sometfs:8080/"
/publishbuild:"vstfs:///Build/Build/6829"
/teamproject:"[Customer].
/platform:"Any CPU"
/flavor:"Debug"
The "TestToolsTask" task is using "MSTest.exe" from "C:\Program Files (x86)\Microsoft Visual Studio 9.0\Common7\IDE\MSTest.exe".

Microsoft (R) Test Execution Command Line Tool Version 9.0.21022.8
Copyright (c) Microsoft Corporation. All rights reserved.

**The type initializer for 'Microsoft.VisualStudio.TestTools.Utility.LicenseHelper' threw an exception.**
 For switch syntax, type "MSTest /help"
MSBUILD : warning MSB6006: "MSTest.exe" exited with code 1. [E:\Blds\73\BuildType\TFSBuild.proj]
The previous error was converted to a warning because the task was called with ContinueOnError=true.
Build continuing because "ContinueOnError" on the task "TestToolsTask" is set to "true".
**Done executing task "TestToolsTask" -- FAILED.**

```

As you see all the tests are passing but after publishing the build calls the [Visual Studio](http://www.microsoft.com/visualstudio/en-us) 2008 version of MSTest.exe. And this got me puzzled. But the error message _s**The type initializer for 'Microsoft.VisualStudio.TestTools.Utility.LicenseHelper' threw an exception.**_ got me really thinking…someone even suggested that I was messing with MS licensing, no WAY I would ever do that!

### The Solution

The solution was really simple, someone in the devteam had created an ordinary BizTalk orchestration project named _**[Customer].[BtsApp].OrchestrationsHelper.Tests**_ but because of that it WAS NOT a unit test project so that’s why the build returned that error.

The best solution would have been to rename the project but after asking the developers that renaming would have costs us a lot of time and refactoring so we decided to exclude that particular assembly from our tests. (details [here]({{ site.baseurl }}{% link _posts/2011-04-15-build-recipe-for-running-unit-tests-using-file-masks-in-tfs-build-2008.md %}))

### Lessons learned

- You should start to think about automated build processes from day one in a dev project.
- Partially succeeded builds can also be a symptom of trying to execute “regular” assemblies through MSTest.

Enjoy!

Hugo

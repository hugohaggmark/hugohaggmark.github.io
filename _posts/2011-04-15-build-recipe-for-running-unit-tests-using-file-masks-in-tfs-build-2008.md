---
layout: post
title: Build recipe for running unit tests using file masks in TFS Build 2008
date: 2011-04-15 08:46:00.000000000 +02:00
categories:
  - BizTalk
  - Resolution
  - Team Build
  - Unit Testing
tags:
  - BizTalk
  - Resolution
  - Team Build
  - Unit Testing
permalink: "/2011/04/15/build-recipe-for-running-unit-tests-using-file-masks-in-tfs-build-2008/"
---

### Background

The background for this build script recipe can be found [here](http://www.hugohaggmark.com/2011/04/15/the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build/ "the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build") and [here](http://www.hugohaggmark.com/2011/04/13/generating-biztalk-binding-files-with-excel-automation-in-a-tfs-build/ "generating-biztalk-binding-files-with-excel-automation-in-a-tfs-build"). Instructions on how to install and configure a build server to run these build scripts can be found [here](http://www.hugohaggmark.com/2011/03/03/building-and-unit-testing-net-4-0-with-team-build-2008-the-correct-order/ "building-and-unit-testing-net-4-0-with-team-build-2008-the-correct-order") and [here](http://www.hugohaggmark.com/2010/11/24/building-and-unit-testing-net-4-0-with-team-build-2008/ "building-and-unit-testing-net-4-0-with-team-build-2008").

### The Challenge

The build recipe has to conform to these constraints/requirements:

- No test lists
- No maintenance of testsettings/testrunconfig files
- Using file mask .Tests to identify which assemblies that contain unit tests
- [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") 2008
- [Visual Studio](http://www.microsoft.com/visualstudio/en-us "Visual Studio") 2010
- BizTalk 2010
- [MSTest](http://en.wikipedia.org/wiki/MSTest "MSTest")

### The Dev.CI.testsettings file

As you might already know the testsettings file (formely know as testrunconfig) defines important configuration that tells MSTest various information; where to look for deployment items for instance.

I create one testsettings file per build that I’ve safely in source control and in that file I add this:

```
<font face="Consolas"><span><font color="#0000ff"><font style="font-size: 9.8pt">&lt;</font></font></span><font style="font-size: 9.8pt"><span><font color="#a31515">Deployment</font></span><span><font color="#0000ff">&gt;</font></span>
<span><font color="#0000ff">  &lt;</font></span><span><font color="#a31515">DeploymentItem</font></span><span><font color="#0000ff"> </font></span><span><font color="#ff0000">filename</font></span><span><font color="#0000ff">=</font></span><span><font color="#0000ff"><a href="file://%5C%5Cbldserv%5CBld%5C1%5CTestResults%5CDeplymentItems%5C">\\bldserv\Bld\1\TestResults\DeplymentItems\</a></font></span><span><font color="#0000ff">/&gt;</font></span>
<span><font color="#0000ff">&lt;/</font></span><span><font color="#a31515">Deployment</font></span></font><span><font style="font-size: 9.8pt" color="#0000ff">&gt;</font></span></font>
```

These lines tells MSTest to look for deployment items in the specified directory/share. This works for now as we only have one build server that executes the [CI-builds](http://en.wikipedia.org/wiki/Continuous_integration "Continuous Integration") but when we add build servers later on we’ll have to come up with a better solution using environment variables for instance.

It would be great if TFS Build could automatically pick up the testsettings file for each solution in the build but as we only do ONE testrun per TFS Build that wouldn’t work either. The best would be if we ran MSTest / solution in the TFS Build.

### The TFSBuild.proj file

Under the ItemGroup for testing I add the following line to tell MSTest to look for testcontainers with the “.Tests.dll” filemask.

<font face="Consolas"><span><font color="#0000ff"><font style="font-size: 9.8pt">&lt;</font></font></span><font style="font-size: 9.8pt"><span><font color="#a31515">TestContainer</font></span><span><font color="#0000ff"> </font></span><span><font color="#ff0000">Include</font></span><span><font color="#0000ff">=</font></span>"<span><font color="#0000ff">$(OutDir)\%2a.Tests.dll</font></span>"<span><font color="#0000ff"> /&gt;</font></span></font></font>

<font face="Consolas"><font style="font-size: 9.8pt"></font></font>

The next step is to introduce a TFS Build override where we specify our testsettings file and do the copying to our deploymentitems folder specified by our testsettings file. There is also delete statement that I was added later because I found an assembly with the correct file mask but that wasn’t a unit test assembly, read more about that adventure [here](http://www.hugohaggmark.com/2011/04/15/the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build/ "the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build").

<font face="Consolas"><span><font color="#0000ff"><font style="font-size: 9.8pt">  &lt;</font></font></span><font style="font-size: 9.8pt"><span><font color="#a31515">Target</font></span><span><font color="#0000ff"> </font></span><span><font color="#ff0000">Name</font></span><span><font color="#0000ff">=</font></span>"<span><font color="#0000ff">BeforeTestConfiguration</font></span>"<span><font color="#0000ff">&gt;</font></span></font></font>

<font color="#0000ff">    &lt;!--</font><font color="#008000">We need to set the path to our xml-files and schema-</font>

<font color="#008000">    files for MSTest to include this can only be done from one </font>

<font color="#008000">    single testsettings-file.</font><font color="#0000ff">--&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">PropertyGroup</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">      &lt;</font><font color="#a31515">RunConfigFile</font><font color="#0000ff">&gt;</font>$(SolutionRoot)Dev.CI.testsettings<font color="#0000ff">&lt;/</font><font color="#a31515">RunConfigFile</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">    &lt;/</font><font color="#a31515">PropertyGroup</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">    </font>

<font color="#0000ff">    &lt;!--</font><font color="#008000">Copy all testinstance-files.</font><font color="#0000ff">--&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">CreateItem</font><font color="#0000ff"> </font><font color="#ff0000">Include</font><font color="#0000ff">=</font>"<font color="#0000ff">$(SolutionRoot)\*\*\TestInstances\*</font>"<font color="#0000ff">&gt;</font>

<font color="#0000ff">      &lt;</font><font color="#a31515">Output</font><font color="#0000ff"> </font><font color="#ff0000">TaskParameter</font><font color="#0000ff"> =</font>"<font color="#0000ff">Include</font>"<font color="#0000ff"> </font><font color="#ff0000">ItemName</font><font color="#0000ff"> =</font>"<font color="#0000ff">MyTestFiles</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">    &lt;/</font><font color="#a31515">CreateItem</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">Copy</font><font color="#0000ff"> </font><font color="#ff0000">SourceFiles</font><font color="#0000ff">=</font>"<font color="#0000ff">@(MyTestFiles)</font>"<font color="#0000ff"> </font>

<font color="#0000ff">          </font><font color="#ff0000">DestinationFolder</font><font color="#0000ff">=</font>"<font color="#0000ff">$(TestResultsRoot)\DeplymentItems</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">    &lt;!--</font><font color="#008000">Copy all schema-files.</font><font color="#0000ff">--&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">CreateItem</font><font color="#0000ff"> </font><font color="#ff0000">Include</font><font color="#0000ff">=</font>"<font color="#0000ff">$(SolutionRoot)\*\*\*.xsd</font>"<font color="#0000ff">&gt;</font>

<font color="#0000ff">      &lt;</font><font color="#a31515">Output</font><font color="#0000ff"> </font><font color="#ff0000">TaskParameter</font><font color="#0000ff"> =</font>"<font color="#0000ff">Include</font>"<font color="#0000ff"> </font><font color="#ff0000">ItemName</font><font color="#0000ff"> =</font>"<font color="#0000ff">MySchemaFiles</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">    &lt;/</font><font color="#a31515">CreateItem</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">Copy</font><font color="#0000ff"> </font><font color="#ff0000">SourceFiles</font><font color="#0000ff">=</font>"<font color="#0000ff">@(MySchemaFiles)</font>"<font color="#0000ff"> </font>

<font color="#0000ff">          </font><font color="#ff0000">DestinationFolder</font><font color="#0000ff">=</font>"<font color="#0000ff">$(TestResultsRoot)\DeplymentItems</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">    &lt;!--</font><font color="#008000">Delete all non unittest-files.</font><font color="#0000ff">--&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">CreateItem</font><font color="#0000ff"> </font>

<font color="#0000ff">      </font><font color="#ff0000">Include</font><font color="#0000ff">=</font>"<font color="#0000ff">$(BinariesRoot)\*\*\[Customer].[BtsApp].OrchestrationsHelper.Tests.dll</font>"<font color="#0000ff">&gt;</font>

<font color="#0000ff">      &lt;</font><font color="#a31515">Output</font><font color="#0000ff"> </font><font color="#ff0000">TaskParameter</font><font color="#0000ff"> =</font>"<font color="#0000ff">Include</font>"<font color="#0000ff"> </font><font color="#ff0000">ItemName</font><font color="#0000ff"> =</font>"<font color="#0000ff">MyNonUnitTestFiles</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">    &lt;/</font><font color="#a31515">CreateItem</font><font color="#0000ff">&gt;</font>

<font color="#0000ff">    &lt;</font><font color="#a31515">Delete</font><font color="#0000ff"> </font><font color="#ff0000">Files</font><font color="#0000ff">=</font>"<font color="#0000ff">@(MyNonUnitTestFiles)</font>"<font color="#0000ff">/&gt;</font>

<font color="#0000ff">  &lt;/</font><font color="#a31515">Target</font><font style="font-size: 9.8pt" color="#0000ff">&gt;</font>

The drawback to this approach is that your testinstance/schema files (often xml-files in BizTalk projects) need to be uniquely named or you would overwrite them in this step.

### Lessons learned

- You should start to think about automated build processes from day one in a dev project.
- We need to find a way to not use hardcoded paths, maybe we could use environment variables.
- Maybe we should look into other testing frameworks like XUnit, NUnit and see if they offer better support for this kind of situation

Please let me know what you think of this solution considering the constraints and requirements at hand. The examples for this post can be found [here](http://www.hugohaggmark.com/wp-content/uploads/2011/04/TFSBuild.zip "TFSBuild.zip").

Cheers!

Hugo

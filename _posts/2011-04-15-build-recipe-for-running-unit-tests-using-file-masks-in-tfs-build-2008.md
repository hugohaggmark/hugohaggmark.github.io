---
layout: post
title: Build recipe for running unit tests using file masks in TFS Build 2008
date: 2011-04-15 08:46:00.000000000 +02:00
tags:
  - BizTalk
  - Resolution
  - Team Build
  - Unit Testing
permalink: "/2011/04/15/build-recipe-for-running-unit-tests-using-file-masks-in-tfs-build-2008/"
---

### Background

The background for this build script recipe can be found [here]({{ site.baseurl }}{% link _posts/2011-04-15-the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build.md %}) and [here]({{ site.baseurl }}{% link _posts/2011-04-13-generating-biztalk-binding-files-with-excel-automation-in-a-tfs-build.md %}). Instructions on how to install and configure a build server to run these build scripts can be found [here]({{ site.baseurl }}{% link _posts/2011-03-03-building-and-unit-testing-net-4-0-with-team-build-2008-the-correct-order.md %}) and [here]({{ site.baseurl }}{% link _posts/2010-11-24-building-and-unit-testing-net-4-0-with-team-build-2008.md %}).

### The Challenge

The build recipe has to conform to these constraints/requirements:

- No test lists
- No maintenance of testsettings/testrunconfig files
- Using file mask .Tests to identify which assemblies that contain unit tests
- [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) 2008
- [Visual Studio](http://www.microsoft.com/visualstudio/en-us) 2010
- BizTalk 2010
- [MSTest](http://en.wikipedia.org/wiki/MSTest)

### The Dev.CI.testsettings file

As you might already know the testsettings file (formely know as testrunconfig) defines important configuration that tells MSTest various information; where to look for deployment items for instance.

I create one testsettings file per build that I’ve safely in source control and in that file I add this:

```xml
<Deployment>
  <DeploymentItem filename=\\bldserv\Bld\1\TestResults\DeplymentItems\/>
</Deployment>
```

These lines tells MSTest to look for deployment items in the specified directory/share. This works for now as we only have one build server that executes the [CI-builds](http://en.wikipedia.org/wiki/Continuous_integration) but when we add build servers later on we’ll have to come up with a better solution using environment variables for instance.

It would be great if TFS Build could automatically pick up the testsettings file for each solution in the build but as we only do ONE testrun per TFS Build that wouldn’t work either. The best would be if we ran MSTest / solution in the TFS Build.

### The TFSBuild.proj file

Under the ItemGroup for testing I add the following line to tell MSTest to look for testcontainers with the “.Tests.dll” filemask.

```xml
<TestContainer Include="$(OutDir)\%2a.Tests.dll" />
```

The next step is to introduce a TFS Build override where we specify our testsettings file and do the copying to our deploymentitems folder specified by our testsettings file. There is also delete statement that I was added later because I found an assembly with the correct file mask but that wasn’t a unit test assembly, read more about that adventure [here]({{ site.baseurl }}{% link _posts/2011-04-15-the-perils-of-using-tests-as-a-file-mask-for-unit-tests-in-tfs-build.md %}).

```xml
<Target Name="BeforeTestConfiguration">
  <!–We need to set the path to our xml-files and schema-files for MSTest to include this can only be done from one single testsettings-file.–>
  <PropertyGroup>
    <RunConfigFile>$(SolutionRoot)Dev.CI.testsettings</RunConfigFile>
  </PropertyGroup>

  <!–Copy all testinstance-files.–>
  <CreateItem Include="$(SolutionRoot)\**\TestInstances\*">
    <Output TaskParameter ="Include" ItemName ="MyTestFiles"/>
  </CreateItem>
  <Copy SourceFiles="@(MyTestFiles)"
        DestinationFolder="$(TestResultsRoot)\DeplymentItems"/>

  <!–Copy all schema-files.–>
  <CreateItem Include="$(SolutionRoot)\**\*.xsd">
    <Output TaskParameter ="Include" ItemName ="MySchemaFiles"/>
  </CreateItem>
  <Copy SourceFiles="@(MySchemaFiles)"
        DestinationFolder="$(TestResultsRoot)\DeplymentItems"/>

  <!–Delete all non unittest-files.–>
  <CreateItem
    Include="$(BinariesRoot)\**\[Customer].[BtsApp].OrchestrationsHelper.Tests.dll">
    <Output TaskParameter ="Include" ItemName ="MyNonUnitTestFiles"/>
  </CreateItem>
  <Delete Files="@(MyNonUnitTestFiles)"/>
</Target>
```

The drawback to this approach is that your testinstance/schema files (often xml-files in BizTalk projects) need to be uniquely named or you would overwrite them in this step.

### Lessons learned

- You should start to think about automated build processes from day one in a dev project.
- We need to find a way to not use hardcoded paths, maybe we could use environment variables.
- Maybe we should look into other testing frameworks like XUnit, NUnit and see if they offer better support for this kind of situation

Please let me know what you think of this solution considering the constraints and requirements at hand. The examples for this post can be found [in TFSBuild.zip]({{ site.baseurl }}/assets/other/TFSBuild.zip).

Cheers!

Hugo

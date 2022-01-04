---
title: Yet another post about Team Build and the 260+ char limit
date: 2011-03-30 12:57:00.000000000 +02:00
tags:
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
permalink: "/2011/03/30/yet-another-post-about-team-build-and-the-260-char-limit/"
---

So this is definitely not a new topic and there has been lots of post about it. But I’d like to make sure that you don’t find yourself in the situation where you receive the following error message:

_"The specified path, file name, or both are too long. The fully qualified file name must be less than 260 characters, and the directory name must be less than 248 characters."_

As many already have stated this is _**not**_ a limit in Team Build but a Windows limit, you can read more about that [in Naming Files, Paths, and Namespaces](<http://msdn.microsoft.com/en-us/library/aa365247(VS.85).aspx>).

Even though many devs out there are brutally aware of this _**hard limit**_ I tend to see the same mistakes appear at customers over and over again.

### Lets start out with some basics:

- Don’t create source control folder hierarchies that mimic your namespace naming or any other convention. That leads to deep hierarchies that will get you closer to the 260+ limit.  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb1.png)
- Instead try to keep your source control hierarchy as flat as possible, use the solution folders feature in [Visual Studio](http://www.microsoft.com/visualstudio/en-us) to represent your hierarchy instead.
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb2.png)
  See how the Source and Tests hierarchy are used as Solution Folders instead in Visual Studio without creating any extra folder level in source control:  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb3.png)
- Keep your workspace folder mappings short and consistent like _**"C:\ws\"**_
- Keep your namespaces short and sweet and remember that you don’t need to name your solution/project folders the same as your namespace (although I know it simplifies the daily work a lot ![Blinkar]({{ site.baseurl }}/assets/images/2011/03/wlEmoticon-winkingsmile1.png))

### And here are some advanced strategies:

- Use $(BuildDefinitionId) instead of $(BuildDefinitionPath) when you configure your build agents.  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb4.png)
  The picture below shows the output in folder structures between these two approaches using the same build definition but different agent settings. The pictures are taken from a [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) 2010 installation for TFS 2008 there is no $(BuildAgentId) folder.  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb5.png)
- In TFS 2008 shorten the “SourcesSubdirectory”, “BinariesSubdirectory” and “TestResultsSubdirectory” configuration in the TfsBuildService.exe.config.
- In TFS 2010 shorten the assignment in the string for the “Initialize Sources Directory”, “Initialize Binaries Directory” and “Initialize TestResults Directory” in your DefaultTemplate.xaml as the picture shows:  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb6.png)
- In TFS 2010 if you’re using the UpgradeTemplate.xaml you can substitute the name for the directories in the Advanced section like the picture shows:  
  ![image]({{ site.baseurl }}/assets/images/2011/03/image_thumb7.png)
- Create a custom Check-In policy that prevents users from checking in structures in source control that are longer then some number set by your administrator. Read more about it [in TFS Max Path mitigation via check-in policy](http://blogs.msdn.com/b/jampick/archive/2008/06/17/tfs-max-path-mitigation-via-check-in-policy.aspx).

This should prevent you from hitting the 260+ hard limit for a while but It’s no silver bullet unfortunately so I hope that a more complete solution will rise soon.

Enjoy,

Hugo

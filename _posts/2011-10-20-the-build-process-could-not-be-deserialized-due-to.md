---
title: The build process could not be deserialized due to…
date: 2011-10-20 15:44:00.000000000 +02:00
tags:
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
permalink: "/2011/10/20/the-build-process-could-not-be-deserialized-due-to/"
---

### Background

At my current customer we’ve upgraded all our builds from [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) 2008 [MSBuild tasks](http://msdn.microsoft.com/en-us/library/z7f65y0d.aspx) to TFS 2010 WorkFlow Activities. I must say that we’ve gone from several common target-files and per-build specific rsp-files to ONE build process template! That says enough about the power of the new build process in TFS 2010.

Following [Microsoft](http://www.microsoft.com/en-us/default.aspx)’s recommendation [ "Understanding a Team Foundation Build System"](http://msdn.microsoft.com/en-us/library/dd793166.aspx#build_controller) we’re using one Build Controller for our Default Collection and we’ve centralized our custom activities to one Team Project; lets call it _**Central**_.

In the Team Project _**Central**_ the custom activities we’re using a stored at _**$/Central/BuildComponents**_ and our Build Controller points out this location as the picture shows below:

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb.png)

In our Custom Activities we have some simple serializble objects that we use as part of our process to store some settings that we need in our build process. An of that would be an XCopyFileWrapper that we use to store 3 string properties:

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb1.png)

We then use this wrapper in our Meta Data property in our DefaultTemplate.xaml file like the picture shown below:

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb2.png)

Then we added the ParamWrapper as an array of XCopyFileWrapper and add it as an argument to DefaultTemplate.xaml like so:

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb3.png)

This way we can have the same DefaultTemplate XCopy files depending on the input we add for each unique build definition in this rather nice centralized GUI for your build definitions:

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb4.png)

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb5.png)

And then in our DefaultTemplate.xaml we have an foreach loop that loops over our array of XCopyFileWrappers and invokes XCopy! This makes our build definitions really sweet, dynamic and expandable right!

## The challenge

So the next Team Project comes along we can call it _**Project B**_ and they want to use our Build Process. So they branch our build process template into their project and they create a new build definition and then KAPOW!

![image]({{ site.baseurl }}/assets/images/2011/10/image_thumb6.png)

What happen here! Well my initial thoughts was another lesson I learned previously and blogged about [Stuck on “Cannot create unknown type {clr-namespace:" in TFS Build?]({{ site.baseurl }}{% link _posts/2011-09-08-stuck-on-cannot-create-unknown-type-clr-namespace-in-tfs-build.md %}) but specifying the assembly in the DefaultTepmplate.xaml didn’t solve this issue.

### The solution

The very simple solution to this problem is that you need to give users READ permissions to your BuildComponents folder in our case the _**$/Central/BuildComponents**_ folder (another way is to solve this is to give other users READ permissions to the _**Central**_ Team Project).

That’s it!

## Reflection

- So if I want to centralize my custom build components then I have to give all users that will use these custom build components READ permission to the folder where the components are stored.
- Or you could have some sort of deployment mechanism that deploys the build components to every Team Project in the Team Project Collection but then you’ll have to have one Build Controller per Team Project.

Cheers,

Hugo

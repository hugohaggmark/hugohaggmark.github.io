---
layout: post
title: How come the add button in Associated Automation isn’t visible in Microsoft
  Test Manager?
date: 2011-01-08 11:56:00.000000000 +01:00
tags:
  - Associated Automation
  - BDD
  - Resolution
  - Team Foundation Server
  - Test Manager
permalink: "/2011/01/08/how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager/"
---

This year is off to a great start with fantastic first week and an even better last day of that week working with my fellow Avega Coach, [Marcus Hammarberg](http://www.marcusoft.net) .

Our work yesterday is clearly summarized by Marcus [in Specification by example with SpecFlow in TFS and the question of traceability](http://www.marcusoft.net/2011/01/specification-by-example-with-specflow.html) and I think Marcus has made some real groundbreaking thoughts on [SpecFlow](http://specflow.org/) together with [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) and [Visual Studio](http://www.microsoft.com/visualstudio/en-us) and I’m very glad to have helped out some parts. A shout out goes to [Håkan Forss](http://hakanforss.wordpress.com/) as he gave both of us some valuable feedback too.

So what’s this post about you might ask? Well during our work we stumbled onto a gotcha with the way Test Manager and associated automation works.

As you can see in the picture below I’ve created a Test Case that some tester later gets to run.

![1]({{ site.baseurl }}/assets/images/2011/01/1_thumb.png "1")

Suppose the tester runs this Test Case and finds a bug. Now we want to assign this bug to a developer who fixes the bug and associates a test with the test case in such way that the test case gets automated.

So we change the automation status to Planned as you can see in the picture below:

![2]({{ site.baseurl }}/assets/images/2011/01/2_thumb.png "2")

### Problem

As you can see the tester can’t set Automation Status in [Test Manager](http://msdn.microsoft.com/en-us/library/ms182409.aspx) to Automated because that state is missing from the list and if we zoom to the right side of the tab control we find that there’s no way of associating any tests either.  
[![2.5]({{ site.baseurl }}/assets/images/2011/01/2.5_thumb.png "2.5")](http://www.hugohaggmark.com/wp-content/uploads/2011/01/2.5.png)

### Solution

Being that this was the first time I looked a this particular feature of [Test Manager](http://msdn.microsoft.com/en-us/library/ms182409.aspx) I must say that I got a little intrigued to say the least. After some Internet searching we still came up with nothing but when we opened up the same Test Case in [Visual Studio](http://www.microsoft.com/visualstudio/en-us) we finally saw the add button (marked with … in the picture below):

[![2.51]({{ site.baseurl }}/assets/images/2011/01/2.51_thumb.png "2.51")](http://www.hugohaggmark.com/wp-content/uploads/2011/01/2.51.png)

### Digging deeper

So it got very clear to me then that this behavior was by design but I still felt that I needed to go behind the scenes to see how this is solved because in the end Test Case is just your average Work Item Type which is defined in XML.

Looking at the Automation Status field in the Test Case WIT XML you’ll see this:

![3]({{ site.baseurl }}/assets/images/2011/01/3_thumb.png "3")

As you can see in the XML you’ll never see the automated state if you haven’t assigned a Microsoft.VSTS.TCM.AutomatedTestId value which is exactly what you do when you associate a test with the add button like the picture below:

![7]({{ site.baseurl }}/assets/images/2011/01/7_thumb.png "7")

Looking at the Associated Automation tab in the Test Case WIT you’ll see this:

![4]({{ site.baseurl }}/assets/images/2011/01/4_thumb.png "4")

As you can see the Associated Automation tab implemented with a control called **AssociatedAutomationControl**. After some investigation with my favorite reflection utility [.Net Reflector](http://www.red-gate.com/products/dotnet-development/reflector/) I found this section marked in red:

![10]({{ site.baseurl }}/assets/images/2011/01/10_thumb.png "10")

It looks like depending on which ServiceProvider that is used when creating the **AssociatedAutomationControl** the Add button will be visible or not, TADA!

To sum things up:

1. You can’t associate automation with unit tests within [Test Manager](http://msdn.microsoft.com/en-us/library/ms182409.aspx)
2. You can associate automation with unit tests within [Visual Studio](http://www.microsoft.com/visualstudio/en-us)
3. All this is because the ServiceProvider associated with the current **AssociatedAutomationControl** controls the visibility of the Add button in the Associate Automation tab.

Cheers!

Hugo

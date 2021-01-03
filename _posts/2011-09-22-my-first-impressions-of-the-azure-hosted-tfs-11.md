---
layout: post
title: My first impressions of the Azure hosted TFS 11
date: 2011-09-22 10:17:34.000000000 +02:00
tags:
  - Team Foundation Server
  - Windows 8
permalink: "/2011/09/22/my-first-impressions-of-the-azure-hosted-tfs-11/"
---

### Background

Have you downloaded and created your own Windows 8 image yet? If not you should really do that, trust me you will love it especially if have a touch enabled laptop. You can easily do this in about 30-60 minutes if you follow my post [here]({{ site.baseurl }}{% link _posts/2011-09-17-the-easiest-way-to-install-and-booting-windows-8-developer-preview-off-a-vhd-virtual-hard-disk.md %}).

So last week was the big launch week during the [Build](http://www.buildwindows.com/) event and Brian Harry announced a lot of great previews such as the Azure hosted TFS 11. You can read more about it in Brian’s post [Team Foundation Server on Windows Azure: A Preview is available!](http://blogs.msdn.com/b/bharry/archive/2011/09/14/team-foundation-server-on-windows-azure.aspx).

As I’m a frequent reader of Brian’s blog I received the post immediately in my RSS reader and luckily for me I was quick enough create my own hosted instance of TFS 11 in Azure using Brian’s activation code.

The irony here is that I created my first hosted TFS 11 in Azure from my Android smartphone, talk about cloud power!

### Metro style makes an entrance

The very first thing that stroke me was the sense of familiarity in the GUI and then of course it struck me, TFS 11 is [Metro](<http://en.wikipedia.org/wiki/Metro_(design_language)>) styled!

#### The Team Project overview with a nice Metro look and feel:

![tfs6]({{ site.baseurl }}/assets/images/2011/09/tfs6_thumb.png)

#### A work item overview with a nice Metro look and feel:

![tfs7]({{ site.baseurl }}/assets/images/2011/09/tfs7_thumb.png)

### The Team concept makes an entrance

There is this long sought concept of a team in TFS 11 that I just started to play around with and it basically means that you can distribute work per team as the team have their “own” schedule, work items, backlog and homepage (landing page). You’ve Always been able to do this your self by manipulating Areas and Iterations but now it’s a first class citizen.

#### Create a team dialog:

![tfs10]({{ site.baseurl }}/assets/images/2011/09/tfs10_thumb.png)

#### Assignment of areas to a specific team:

![tfs11]({{ site.baseurl }}/assets/images/2011/09/tfs11_thumb.png)

#### Assignment of iterations to a specific team:

![tfs12]({{ site.baseurl }}/assets/images/2011/09/tfs12_thumb.png)

### Iterations with dates enters

As you can see above my first iteration (Sprint1) has a Start Date and an End Date which is also a really nice feature that I’ve been missing for ever. Thank you Microsoft for providing this in the box!

Then I found this Current Sprint folder in the Shared Queries section of Work Items and my guess is that there will be a job that automatically adjusts the Work Item Queries according to your dates. I haven’t been able to prove this yet, but I will update this section with my findings.

#### Showing Work Items section in the new Team Explorer:

####

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb24.png)

### Search Work Items in Visual Studio makes an entrance

If you read my last post about finding stuff in TFS [How do I find stuff in TFS 2010?]({{site.baseurl}}{% link _posts/2011-09-09-how-do-i-find-stuff-in-tfs-2010.md %}) you can see that I wanted a nice search for Work Items functionality directly in the IDE and now I have it. Great job! The nice thing about it is if you enter a Work Item ID that you know about Visual Studio will open it for you.

#### Entering a Work Item ID in the search field will get you that Work Item:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb25.png)

Another great feature is that you can turn your searches directly to a Query with the Open as Query option as shown below.

#### Open as Query enables you to store your searches as queries:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb26.png)

This is how far my explorations have taken me so far and will create more posts as I go along discovering more features.

Cheers,

Hugo

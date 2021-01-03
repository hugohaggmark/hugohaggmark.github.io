---
layout: post
title: "Logon failure: the user has not been granted the requested logon type at this
  computer"
date: 2010-11-20 11:12:00.000000000 +01:00
tags:
  - Error
  - Resolution
  - Team Foundation Server
permalink: "/2010/11/20/logon-failure-the-user-has-not-been-granted-the-requested-logon-type-at-this-computer/"
---

I’m currently on an engagement where I’m responsible for the design, architecture and implementation of TFS 2010 at a large Swedish financial institute.

As a first step we’re pair installing (me coaching and another guy doing the work) and configuring a TFS 2010 POC to make sure we identify all the potential challenges with the customers specific environment like AD, Policies etc.

Very well we make sure that we follow the exact installation instructions [from "Team Foundation Installation Guide for Visual Studio 2010"](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=2d531219-2c39-4c69-88ef-f5ae6ac18c9f) and start our work installing SQL Server, SharePoint Foundation, TFS and lastly we configure TFS with the great new administration console. Everything looks great and no problems so far!

As a rule of thumb I always create a team project for each of the process templates that come with TFS to make sure that everything is truly working (so should you). Anyway the team project creation went perfect!

So before I go ahead and try to check-in some great code in Source Control I always try to connect to my team project portal to makes sure everything looks great, KABOOOOM!

Before we started this particular installation I told the guy I was pair installing with that if something was to go wrong it would most certainly be issues with connection to SQL Server Reporting Services. And as you can see below I was right:

![image]({{ site.baseurl }}/assets/images/2010/11/image_thumb.png "image")

_Note: all pictures are taken from my own environment and not from customer_

Here we go again I thought for myself…

I won’t bore you with all the details but after some investigation and after consulting my own virtualized TFS farm (on my bootable vhd by the way, read post [here]({{ site.baseurl }}{% link _posts/2010-11-15-creating-a-bootable-vhd-the-easy-waycontinued.md %})) I found that although we had granted the “reports” account the “ **Allow log on locally** ” as instructed there was a domain policy that **denied** the “reports” account the same rights as you can see in the picture below.

![image]({{ site.baseurl }}/assets/images/2010/11/image_thumb1.png "image")

_Note: all pictures are taken from my own environment and not from customer_

We hade the AD-department make an exception for this account on this computer and just like that our problem was solved.

![image]({{ site.baseurl }}/assets/images/2010/11/image_thumb2.png "image")

_Note: all pictures are taken from my own environment and not from customer_

This is just one solution for this type of problem and there are others like the password for the “reports” account being wrong etc. so make sure you check for other issues as well.

I’m sure I will remember to check for domain policies overriding the local policy the next time.

Cheers!

Hugo

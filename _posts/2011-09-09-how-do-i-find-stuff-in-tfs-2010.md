---
layout: post
title: How do I find stuff in TFS 2010?
date: 2011-09-09 13:32:00.000000000 +02:00
categories:
  - Team Foundation Server
  - Tips
tags:
  - Team Foundation Server
  - Tips
permalink: "/2011/09/09/how-do-i-find-stuff-in-tfs-2010/"
---

## Background

The other day I was coaching a colleague and I got the excellent question about how to find stuff in [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") 2010. So I explained to my colleague about some of the different ways you can find stuff and realized that this would be an excellent and hopefully informative post for me and others.

## Scenario 1: you know the Id of the Work Item you’re looking for

If you’ve ever worked with TFS for a while you’ll (hopefully) start to memorize (unconsciously) the unique Id’s for the tasks, bugs, user stories, test cases e.t.c. that you work with. If this is your case then you have 3 very fast ways of accessing these Work Items fast.

### Inside [Visual Studio](http://www.microsoft.com/visualstudio/en-us "Visual Studio")

1. Right-click the Work Items node and choose Go to Work Item[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb5.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image5.png)
2. …enter an Id  
   [![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb6.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image6.png)
3. and wooops there it is.  
   [![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb7.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image7.png)

Or if mouse clicking isn’t your way of life add a keyboard shortcut that does the same thing as shown below:  
[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb8.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image8.png)

### Inside the Team Portal and Team Web Access

On your Team Project Portal you should have a textbox that will do the same as the Go to Work Item above inside Visual Studio. It will look something like this:

[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb9.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image9.png)

The exact same textbox is present in Team Web Access and look like this:

[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb10.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image10.png)

## Scenario 2: you don’t know the Id of the Work Item you’re looking for

Let’s pretend that you don’t remember your Id’s or perhaps you’re a new team member on an ongoing Team Project, what do you do then?

Well you could go through all of the work items and search for the ones you’re really interested in ![Ler]({{ site.baseurl }}/assets/images/2011/09/wlEmoticon-smile1.png) or you could search for Work Items. This is in my experience a somewhat unknown functionality that I would really love to have inside Visual Studio but if you go your Team Web Access you can actually search for words in title or description fields as shown below:

[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb11.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image11.png)

## Scenario 3: you need to filter out an massive amount of Work Items

The best way to find, filter and structure you Work Items is to create good Team Queries, and I will not go into details how you accomplish this but I will give you a great tip.

1. Take an existing Work Item Query and choose Edit Query
2. Change it the way you want it
3. Save it as your own Work Item Query or as a new Team Query (use folders to create even better structure)

Another great tip is to open your Work Item query in Excel and then use the power of Excel to filter stuff.

[![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb12.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/09/image12.png)

## Scenario 4: use the power of search in [SharePoint](http://sharepoint.microsoft.com/en-us/Pages/default.aspx "SharePoint")

The best way to find information in SharePoint items and in documents on your Team Portal is to use the awesome powers of SharePoint Search. Here is a [sheet](http://download.microsoft.com/download/6/6/B/66B69E5C-0010-4D8E-8DB9-C7120E818654/SharePoint%202010%20Search%20Datasheet.pdf "SharePoint 2010 Search Sheet") explaining some of the cool features in the box.

Enjoy,

Hugo

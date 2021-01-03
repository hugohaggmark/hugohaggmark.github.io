---
layout: post
title: TFS 2010 better with SharePoint
date: 2011-02-17 21:55:00.000000000 +01:00
tags:
  - SharePoint
  - Team Foundation Server
permalink: "/2011/02/17/tfs-2010-better-with-sharepoint/"
---

Lately I’ve got some questions concerning [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") 2010 with or without [SharePoint](http://sharepoint.microsoft.com/en-us/Pages/default.aspx "SharePoint") and this blog post does a fair attempt to answers some of those questions. If you don’t have any hesitations and like to use SharePoint you should read this [post]({{ site.baseurl }}{% link _posts/2010-11-08-differences-between-tfs-2010-on-sharepoint-foundation-and-sharepoint-server.md %} "differences-between-tfs-2010-on-sharepoint-foundation-and-sharepoint-server") to figure out which version of SharePoint you should use.

## Background

SharePoint is no longer a required component when you install TFS 2010 and therefore some customers are asking questions about having SharePoint integrated with TFS 2010 or not.

## Dashboards

You wouldn’t buy a car without a dashboard or would you?

If we take a look at the Dashboard for the MSF Agile process template you’ll should see something like so:

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb.png "image")

### Speed

You have detailed information of your projects current speed much like the speedometer in your car like so:

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb1.png "image")

### Work Item overview

You get an overview of the contents of your project like so:

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb2.png "image")

You could compare this to your gas gauge in your car or any other information gauges that tell you detailed information about the car.

### Builds

Visualize the state of your build for all projectmembers and answers questions like why are builds breaking by drilling down to the individual builds directly from SharePoint.

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb3.png "image")

### Checkins

You also get an overview of the latest checkins and you drill into the details directly from SharePoint.

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb4.png "image")

### Work with your Work Items

You can even get down and work with your Work Items directly from SharePoint in various ways:

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb5.png "image")

Interested in a specific Work Item? Use this shortcut:

![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb6.png "image")

## Document Management

You get a state of the art document management system with lots and lots of possibilities and customizations that will make the management of your project documents a joy. Read more about it [here](http://technet.microsoft.com/en-us/library/cc261933.aspx "Document management overview (SharePoint Server 2010)").

## Search

With SharePoint comes a great search engine that will help you search for information throughout your team portal.

## And a whole lot more

Blogs, Wikis, announcements, calendars and a whole lot more goodies are there to make your collaboration experience the best.

## Summary

TFS is a great product without your team portal in SharePoint but it’s truly the best collaboration platform for development projects in the world with SharePoint and I highly recommend you installing it when you install TFS 2010.

Have a nice one!

Hugo

---
layout: post
title: BDD Bonanza with SpecFlow, SpecLog and TFS, huh?
date: 2011-02-24 16:37:00.000000000 +01:00
tags:
  - BDD
  - SpecFlow
  - SpecLog
  - Team Foundation Server
permalink: "/2011/02/24/bdd-bonanza-with-specflow-speclog-and-tfs-huh/"
---

## Confession

I must admit that title is somewhat provoking and that will certainly get some people really going. Anyway from a [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") perspective I see real potential in the integration between SpecLog and TFS and I hope to show this in upcoming posts.

## Background

I’ve been using [SpecFlow](http://specflow.org/ "SpecFlow") a couple of months and I really like the feeling I get every time a debug my specifications ![Ler]({{ site.baseurl }}/assets/images/2011/02/wlEmoticon-smile.png). A colleague of mine [Marcus Hammarberg](http://www.marcusoft.net "Marcus Hammarberg") has written lot’s of posts like [this](http://www.marcusoft.net/2011/02/specification-by-example-with.html "http://www.marcusoft.net/2011/02/specification-by-example-with.html") and at some point we even did a joint venture that resulting in this [post](http://www.marcusoft.net/2011/01/specification-by-example-with-specflow.html "http://www.marcusoft.net/2011/01/specification-by-example-with-specflow.html") by Marcus and this one by [me]({{ site.baseurl }}{% link _posts/2011-01-08-how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager.md %} "{{ site.baseurl }}{% link _posts/2011-01-08-how-come-the-add-button-in-associated-automation-isnt-visible-in-microsoft-test-manager.md %}").

So we had quite a good story for developers at that point and really no nice way to present specification files to testers or business analysts. Well the same team that brought you SpecFlow has also made [SpecLog](http://www.speclog.net/ "SpecLog") a tool that looks very promising.

## Installation

The installation is not completely automated there’re a some manual steps you need to do to make the TFS Integration work. This is certainly a feature I’d really like [TechTalk](http://www.techtalk.at/ "TechTalk") to provide in future releases.

If you are using the MSF Agile process template make sure you’ve downloaded version TechTalk.SpecLog.1.0.63.msi or later. When I couldn’t get the integration to work the guys at support helped me out and after that weekend they released a working version, very impressive indeed!

### Installing the MSI

Make sure you’ve logged in on your TFS server. Run the downloaded msi and make sure you select all components like this picture:  
[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb7.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image7.png)

### Create service account

Create a service account like the picture below:[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb8.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image8.png)

Give the same service account Log on as a Service rights as the picture shows.

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb9.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image9.png)

After this you have to start a command prompt as Adminstrator and do the following:

- Add the created account to the [TEAMFOUNDATION]\Team Foundation Service Accounts with the following command  
  tfssecurity /g+ "Team Foundation Service Accounts" n:[YOURDOMAIN]\SpecLogSvc ALLOW /server:http://yourtfsserver:8080/tfs
- Making it possible for the service account to open TCP port 4242 with the following command  
  netsh http add urlacl url=[http://+:4242/](http://+:4242/) user=[YOURDOMAIN]\SpecLogSvc

### Create a shared folder

Create a shared folder that gives [YOURDOMAIN]\SpecLogSvc contribute rights to that folder like so:

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb10.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image10.png)

### Follow the instructions given from TechTalk

In your SpecLog folder you will have two folders, one called **Server** and one called **TFSIntegration. I** n these folders there’re txt-files that will guide you through the next manual steps you need to get started. Start with server instructions first.

### Gotchas

- The default log path is under %LOCALAPPDATA%\SpecLog so I changed it to the same shared folder created earlier.
- When you create the windows services you’ll be prompted with username and password. It’s very important to enter the username in the format [**YOURDOMAIN**]\[**ServiceAccount**]. Otherwise you’ll get an error.

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb11.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image11.png)

### Testing

You have to connect to a shared repository to get the TFS integration working like the picture shows below:

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb12.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image12.png)

Next steps are to start working on your requirements ![Skrattar]({{ site.baseurl }}/assets/images/2011/02/wlEmoticon-openmouthedsmile.png)! Well I’ve made a simple screen capture that shows how it could look.

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb13.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image13.png)

And If we take a look at the US3 user story we will find this nice overlook. Notice how you can see the TFS source controlled Gherkin file connected to this User Story.

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb14.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image14.png)

The same User Story would look like this in [Visual Studio](http://www.microsoft.com/visualstudio/en-us "Visual Studio"):

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb15.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image15.png)

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb16.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image16.png)

And as you can see the integration works...the service account does all the job:

[![image]({{ site.baseurl }}/assets/images/2011/02/image_thumb17.png "image")](http://www.hugohaggmark.com/wp-content/uploads/2011/02/image17.png)

I must say that things are truly starting to come full circle with SpecLog and I hope that my small post will get you started with SpecLog integration with TFS.

I’ll sure keep working with SpecLog, TFS and most importantly people,

Hugo

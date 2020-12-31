---
layout: post
title: Mysterious error from BTSTask.exe
date: 2010-09-24 19:24:54.000000000 +02:00
categories:
  - BizTalk
  - Team Build
  - Team Foundation Server
tags:
  - BizTalk
  - Error
  - Team Build
  - Team Foundation Server
permalink: "/2010/09/24/mysterious-error-from-btstask-exe/"
---

So I'm currently working with a customer/partner that needs some help with building, deploying and testing [BizTalk 2009](http://www.microsoft.com/biztalk/en/us/roadmap.aspx "BizTalk 2009") applications with [TFS 2008](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx "Team Foundation Server 2008") (other post will describe that solution) and as usual I&nbsp;start off in my own virtual environment where I test building, deploying and testing before I deliver the builds to the customer.

Anyway everything is working exactly like my own environment until the build script hits the "export BizTalk 2009 application as msi" section. I get this funny error message:

<address>BizTalk Server Deployment </address><address>Error while exporting application  &lt;my application&gt; into MSI package. Function failed during execution...</address><address></address>

So the first thing I try is running the same command that TFSBuild just ran in my own Command Prompt, and of course it runs without any errors. So I start looking for stuff that differs between my account and the TFSBuild service account. I go through all the&nbsp;permissions&nbsp;and security groups without any luck. So I continue with my search in the TFSBuild log and find that the temp directory for the service account looks a bit strange:

C:\users\ \<TFSBuild Service Account\>\Tmp\...

Can you see the problem?

There was a **space** in the name for the TFSBuild service account and that made the ExportApp function of [BTSTask](<http://msdn.microsoft.com/en-us/library/aa559686(BTS.10).aspx> "BTSTask") to fail. The solution in my case was to set the environment variables TEMP and TMP to nicer paths like C:\Temp or C:\Tmp and the build worked.

So don't use spaces in your account names because you never know what problems you'll run into.

Cheers!

Hugo

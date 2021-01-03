---
layout: post
title: Mysterious error from BTSTask.exe
date: 2010-09-24 19:24:54.000000000 +02:00
tags:
  - BizTalk
  - Error
  - Team Build
  - Team Foundation Server
permalink: "/2010/09/24/mysterious-error-from-btstask-exe/"
---

So I'm currently working with a customer/partner that needs some help with building, deploying and testing [BizTalk 2009](http://www.microsoft.com/biztalk/en/us/roadmap.aspx) applications with [TFS 2008](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx) (other post will describe that solution) and as usual I start off in my own virtual environment where I test building, deploying and testing before I deliver the builds to the customer.

Anyway everything is working exactly like my own environment until the build script hits the "export BizTalk 2009 application as msi" section. I get this funny error message:

```
BizTalk Server Deployment

Error while exporting application <my application> into MSI package. Function failed during execution...
```

So the first thing I try is running the same command that TFSBuild just ran in my own Command Prompt, and of course it runs without any errors. So I start looking for stuff that differs between my account and the TFSBuild service account. I go through all the permissions and security groups without any luck. So I continue with my search in the TFSBuild log and find that the temp directory for the service account looks a bit strange:

```
C:\users\ \<TFSBuild Service Account\>\Tmp\...
```

Can you see the problem?

There was a **space** in the name for the TFSBuild service account and that made the ExportApp function of [BTSTask](<http://msdn.microsoft.com/en-us/library/aa559686(BTS.10).aspx>) to fail. The solution in my case was to set the environment variables TEMP and TMP to nicer paths like _**C:\Temp**_ or _**C:\Tmp**_ and the build worked.

So don't use spaces in your account names because you never know what problems you'll run into.

Cheers!

Hugo

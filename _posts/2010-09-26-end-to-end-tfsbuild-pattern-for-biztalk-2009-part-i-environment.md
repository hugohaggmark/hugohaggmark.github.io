---
layout: post
title: End to end TFSBuild pattern for BizTalk 2009, part 1 (Environment)
date: 2010-09-26 08:37:58.000000000 +02:00

tags:
  - BizTalk
  - Development Environment
  - Team Build
  - Team Foundation Server
permalink: "/2010/09/26/end-to-end-tfsbuild-pattern-for-biztalk-2009-part-i-environment/"
---

If you read my earlier blog post about a mysterious error from BTSTask you already know that I'm currently helping a customer with automated TFS 2008 builds for BizTalk 2009, if you haven't read my earlier post here's your [chance]({{ site.baseurl }}{% link _posts/2010-09-24-mysterious-error-from-btstask-exe.md %} "mysterious error from btstask").

### Environment

Firstly you'll have to setup some sort of build server and there's at least 3 ways you can do this:

- Have the build server installed separate from the "build BizTalk server"
- Have the build server start a virtual snapshot of a "build BizTalk server"
- Have the build server installed on the "build BizTalk server"
- ???

I typically skip the first option because it usually end up in me having to write some kind of remote execution scripts ([PSExec](http://technet.microsoft.com/en-us/sysinternals/bb897553.aspx "PSExec")) or starting the scripts with timer jobs. Either way the overall build pattern turns out more complex than it needs to be or managing the build (yes you will be managing your builds) will be harder.

The second option would be the top choice for me if the customer has [TFS 2010](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx "TFS 2010") and [Lab Management](http://msdn.microsoft.com/en-us/vstudio/ee712698.aspx "Lab Management") in place. I know there are virtualization options for [TFS 2008](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx "TFS 2008") but I've never user them myself.

So this leaves my with the option to have the "build BizTalk server" and the build server on the same box which makes it a lot easier.

Remember that you'll need to install and configure the BizTalk server and remember to install/configure these options during the installation of the BizTalk server:

- **Developer Tools and SDK**
- **Additional Software / Project Build Component** , build component used by TFSBuild.

When the BizTalk server is installed and configured you'll need to make/configure these options:

- The service account for TFSBuild need to be added to the local **Administrators** group, required to install/uninstall assemblies to the GAC.
- The service account for TFSBuild need to be added to the **BizTalk Administrators** group, required to use BTSTask operations.

As a common practice I always create a Hello World build type to test that my build server is working correctly before moving on with any advanced build patterns with automatic deploy for instance.

Hope you'll enjoy this first part of the series!

Hugo

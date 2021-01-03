---
layout: post
title: Build partially succeeded, but hey no (obvious) errors!
date: 2010-12-08 17:50:00.000000000 +01:00
categories:
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
  - Test impact analysis
tags:
  - Error
  - Resolution
  - Team Build
  - Team Foundation Server
  - Test impact analysis
permalink: "/2010/12/08/build-partially-succeeded-but-hey-no-obvious-errors/"
---

The other day I was trying out the new excellent “Test impact analysis” feature in Visual Studio - Team Foundation Server 2010 and I stumbled upon a rather mysterious problem as you can see from the screenshot below.

![2010-12-04 10-46-21]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-46-21_thumb.png "2010-12-04 10-46-21")

The build says “Build partially succeeded” but as you can see I had NO build errors and ALL (one) my tests where passing!?!

So the first thing I did was to check the build log by clicking the link View Log as you can se below. ![2010-12-04 10-32-13]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-32-13_thumb.png "2010-12-04 10-32-13")

By the way I really enjoy the new look and feel in Team Foundation Build, its precise, gives me a lot of valuable information and it gives me access to the most common command.  
Anyway I browsed through the log and found this little piece of information hidden amongst all the other good stuff.

![2010-12-04 10-40-14]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-40-14_thumb.png "2010-12-04 10-40-14")

Ok, I can see that I have some sort of **Test Run Error** so my first instinct was to run another build. But this time I was going to use another great new feature in Team Foundation Build i.e. the ability to set the **_type of logging verbosity_** needed for the build. I started a new build but this time I chose the Diagnostic level of logging instead of normal. Take a look at the picture below:

![2010-12-04 10-38-28]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-38-28_thumb.png "2010-12-04 10-38-28")

Unfortunately the Diagnostic level didn’t help me much with the problem at hand and it took me a while before I found that my “Test Results” window was minimized and in that window was the solution to my whole mystery. As you can see when I opened the “Test Results” window there is a warning and a link to the problem.

![2010-12-04 10-41-48]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-41-48_thumb.png "2010-12-04 10-41-48")

Following that link gave me the precise clue to the problem:

![2010-12-04 10-42-54]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-42-54_thumb.png "2010-12-04 10-42-54")

and the solution was easy to fix:

![2010-12-04 10-43-44]({{ site.baseurl }}/assets/images/2010/12/2010-12-04-10-43-44_thumb.png "2010-12-04 10-43-44")

So remember keep the “Test Results” opened at all times and you’ll avoid spending time chasing mysterious non existing errors.

Enjoy!

Hugo

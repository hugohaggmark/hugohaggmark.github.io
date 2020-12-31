---
layout: post
title: Do you get a Login dialog from your client machine when connecting to SSRS
  using all but the IP-address?
date: 2011-12-22 13:43:00.000000000 +01:00
categories:
  - Reporting Services
  - SSRS
  - Team Foundation Server
tags:
  - Error
  - Reporting Services
  - Resolution
  - SSRS
  - Team Foundation Server
permalink: "/2011/12/22/do-you-get-a-login-dialog-from-your-client-machine-when-connecting-to-ssrs-using-all-but-the-ip-address/"
---

## Background

I’ll be leaving my current client soon after been there for more then a year. As a final test the person at the customer that shadowed me during all this time had to install a complete [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362 "TFS") environment including build server without my help.

And he did it, which I personally take as the best compliment as a coach and consultant leaving the assignment. Anyway my job in this final test was to verify that everything was working as expected.

## Challenge

When I reached the verification part in my tests where I finally tried to connect to SSRS from my client machine I got an Login Dialog every time except when I used the IP-address of the SSRS machine.

As a seasoned installer of TFS and SSRS I thought I had seen all the silly errors you can get configuring SSRS so instantly I got the feeling that something was rotten outside the SSRS box. And there was some issues with the DNS that were outside the control of the SSRS box. After almost 1½ days (I kid you not) I stumbled upon the solution…

## Solution

Someone had added a <font face="Consolas"><span style="color: "><font color="#0000ff">&lt;</font></span><span style="color: "><font color="#a31515">RSWindowsNegotiate</font></span><span style="color: "><font color="#0000ff">/&gt; </font></span></font><font color="#000000">tag to the authentication types section in the rsreportserver.config file (<a title="RSReportServer Configuration File" href="http://msdn.microsoft.com/en-us/library/ms157273.aspx">located in your SSRS installation path</a>). As we’re not using kerberos in this installation a quick delete of the <font face="Consolas"><span style="color: "><font color="#0000ff">&lt;</font></span><span style="color: "><font color="#a31515">RSWindowsNegotiate</font></span><span style="color: "><font color="#0000ff">/&gt; </font></span></font></font><font color="#333333">tag fixed our issue. </font><font color="#000000"> This is how our authentication types section looked like after the change.</font>

```
<font face="Consolas"><span style="color: "><font color="#0000ff">&lt;</font></span><span style="color: "><font color="#a31515">AuthenticationTypes</font></span><span style="color: "><font color="#0000ff">&gt;</font></span>
<span style="color: "><font color="#0000ff"> &lt;</font></span><span style="color: "><font color="#a31515">RSWindowsNTLM</font></span><span style="color: "><font color="#0000ff">/&gt;</font></span>
<span style="color: "><font color="#0000ff">&lt;/</font></span><span style="color: "><font color="#a31515">AuthenticationTypes</font></span><span style="color: "><font color="#0000ff">&gt;</font></span>
</font>
```

<font color="#000000">Now this configuration was probably done by the IT-ops that delivered the machine in the first place but investigation will continue to exclude my fellow shadower from blame.</font>

Cheers,

Hugo

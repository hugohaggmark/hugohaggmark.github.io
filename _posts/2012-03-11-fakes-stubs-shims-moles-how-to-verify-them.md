---
layout: post
title: Fakes, Stubs, Shims, Moles and how to verify them?
date: 2012-03-11 10:47:51.000000000 +01:00
categories:
  - Bootable VHD
  - Fakes
  - Moles
  - SharePoint
  - TDD
  - Unit Testing
  - Windows 8
tags:
  - Bootable VHD
  - Fakes
  - Moles
  - SharePoint
  - TDD
  - Unit Testing
  - Windows
permalink: "/2012/03/11/fakes-stubs-shims-moles-how-to-verify-them/"
---

### Background

If you have been following my posts [here](http://www.hugohaggmark.com/2012/01/04/how-do-i-do-moles/ "How Do I Do Moles?")and [here&nbsp;](http://www.hugohaggmark.com/2011/09/12/how-i-used-moles-in-a-real-life-biztalk-scenario/ "How I used Moles in a real life BizTalk scenario")before you probably know that when I have to I use [Moles](http://research.microsoft.com/en-us/projects/pex/)from [Microsoft](http://www.microsoft.com) Research. So the other day a friend of mine mentioned this new cool thing called [Fakes, Stubs and Shims](<http://msdn.microsoft.com/en-us/library/hh549175(v=vs.110).aspx>) in [Visual Studio 11](http://www.microsoft.com/visualstudio/11/en-us)&nbsp;so I decided to look into that.

The first thing I actually did was to ask&nbsp;the following&nbsp;question on Twitter because I was curious to see if Moles and Fakes were similar:

[![]({{ site.baseurl }}/assets/images/2012/03/Twitter1.png "Twitter1")](http://www.hugohaggmark.com/wp-content/uploads/2012/03/Twitter1.png)

###

###

###

###

###

###

###

Ok so Fakes in VS 11 are derived from Moles. My next question to&nbsp;Peter was one I've been thinking about alot lately:

### [![]({{ site.baseurl }}/assets/images/2012/03/Twitter2.png "Twitter2")](http://www.hugohaggmark.com/wp-content/uploads/2012/03/Twitter2.png)

So Microsoft are looking into adding verify support to Fakes, great!!! this means&nbsp;that&nbsp;you don't have to use this hack going forward :) Thanks Peter for that fast feedback!

For all you using Moles this hack will still apply. And for all you [SharePoint](http://sharepoint.microsoft.com/en-us/Pages/default.aspx "SharePoint")developers out there; YES this is for you guys because you need to be doing more TDD!

My next step was to create a bootable vhd with [Windows 8 Consumer Preview](http://windows.microsoft.com/en-US/windows-8/download "Windows 8 Consumer Preview") and [Visual Studio 11](http://www.microsoft.com/visualstudio/11/en-us)&nbsp;using my preferred method [here](http://www.hugohaggmark.com/2011/09/17/the-easiest-way-to-install-and-booting-windows-8-developer-preview-off-a-vhd-virtual-hard-disk/ "The easiest way to Install and Boot Windows 8 Developer Preview off a VHD (Virtual Hard Disk)")&nbsp;and start hacking away.

### Step 1

Get up to speed with Fakes, Stubs, Shims&nbsp;by reading "Using shims to isolate calls to non-virtual functions in unit test methods" found [here](<http://msdn.microsoft.com/en-us/library/hh549176(v=vs.110).aspx> "Using shims to isolate calls to non-virtual functions in unit test methods"); go on I'll wait until you're finished.

You'll probably end up with a test method looking like the one shown below:

[csharp]  
 [TestClass]  
 public class Y2KCheckerTests_Step1  
 {  
 [TestMethod]  
 [ExpectedException(typeof(ApplicationException))]  
 public void Check_WhenCalledWithTheDate_2000_1_1_ThenApplicationExceptionIsThrowned()  
 {  
 using(ShimsContext.Create())  
 {  
 ShimDateTime.NowGet = () =\> { return new DateTime(2000, 1, 1); };

Y2KChecker.Check();  
 }  
 }  
 }  
[/csharp]

Well this is all fine, but what if you have a thousand tests that want to detour DateTime.Now? If you read my previous post [here](http://www.hugohaggmark.com/2012/01/04/how-do-i-do-moles/ "How Do I Do Moles?") I introduced a Testable object that encapsulates behaviour.

### Step 2

So the next step is to&nbsp;encapsulate this into a Testable object that I call Y2KShimDateTime like the code below shows:

[csharp]  
[TestClass]  
public class Y2KCheckerTests_Step2  
{  
 [TestMethod]  
 [ExpectedException(typeof(ApplicationException))]  
 public void Check_WhenCalledWithY2KShimDateTime_ThenApplicationExceptionIsThrowned()  
 {  
 using (ShimsContext.Create())  
 {  
 Y2KShimDateTime shim = new Y2KShimDateTime();

Y2KChecker.Check();  
 }  
 }  
}

public class Y2KShimDateTime  
{  
 public Y2KShimDateTime()  
 {  
 ShimDateTime.NowGet = () =\> { return new DateTime(2000, 1, 1); };  
 }  
}  
[/csharp]

Wow now we're getting somewhere. We can reuse our Y2KShimDateTime in all our test methods. But hey what if we really just want to verify that a specific method has been called? You could just add some counter to the Y2KShimDateTime and call a property like the recommendation [here](http://stackoverflow.com/questions/6943332/how-to-assign-opt-from-multiple-delegates-for-a-moled-method/6949941#6949941).

### Step 3

I use [Moq](http://code.google.com/p/moq/ "The simplest mocking library for .NET 3.5 and Silverlight with deep C# 3.0 integration")&nbsp;to Mock my code because it's simple and I love the way I can control return values with setups but mostly I love the way I can verify that a certain method was called with a specific value! If you haven't looked at Moq yet I strongly encourage you to do so!

To accomplish this with Fakes, Shims or Moles you will need to trick Moq a bit (a hack I know). First of all let's take a look at my verifiable Y2KShimDateTime class called **VY2KShimDateTime**.

[csharp]  
public class VY2KShimDateTime  
{  
 public void InitFake()  
 {  
 ShimDateTime.NowGet = () =\>  
 {  
 return VNowGet();  
 };  
 }

public virtual DateTime VNowGet()  
 {  
 return new DateTime();  
 }  
}  
[/csharp]

The first part of the trick is to move the initialization&nbsp;of the detour from the constructor to a public method. This enables us to initialize the detours from Moq as you will see soon. The next thing is to create a public virtual method (VNowGet in this case) that will be called by Moq. There you have it! Easy enough?

Lets see how we use the **VY2KShimDateTime** from Moq.

```csharp; highlight: [4]
using (ShimsContext.Create()) { Mock mock = new Mock(); mock.Object.InitFake(); mock.Setup(v =\> v.VNowGet()).Returns(new DateTime(2000, 1, 1)); Y2KChecker.Check(); }
```

As you can see from the highlighted row above we're initializing our detours after we create our mock this way we can be sure our detours will work. And now we can use the usual setup from Moq to Control what our detour will return. Brilliant don't you say?

Let's throw a verify into this equation and look how that looks.

[csharp]  
using (ShimsContext.Create())  
{  
 Mock mock = new Mock();  
 mock.Object.InitFake();  
 mock.Setup(v =\> v.VNowGet()).Returns(new DateTime(1, 1, 1));

Y2KChecker.Check();

//Uncomment line below to see verify that mock.Verify throws correct message  
 //Y2KChecker.Check();

mock.Verify(v =\> v.VNowGet(), Times.Once());  
}  
[/csharp]

In this case we'll have to supply a date other than Y2K because otherwise we'll get an exception but as you can see in the verify method we verify that DateTime.Now is called only once. If I would to uncomment the second Y2KChecker.Clean(); call the verify on our mock would throw an exception.

### Summary

I've showed how to create verifiable Fakes, Stubs and Shims (works for Moles as well) using a small hack. I use this approach daily&nbsp;when I work with&nbsp;Moles because I really like the combination of Moq verify and setup and Fakes/Moles isolation and detouring.

Hopefully Microsoft will incorporate this in a near future in Fakes but until then give this a try.

All code can be downloaded&nbsp;from GitHub [here](https://github.com/hugohaggmark/HugoHaggmark.Blog "Github").

Cheers,

Hugo

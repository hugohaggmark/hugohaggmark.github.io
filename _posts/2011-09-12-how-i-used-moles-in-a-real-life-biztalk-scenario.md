---
layout: post
title: How I used Moles in a real life BizTalk scenario
date: 2011-09-12 11:14:00.000000000 +02:00
tags:
  - BizTalk
  - Moles
  - TDD
  - Tools
permalink: "/2011/09/12/how-i-used-moles-in-a-real-life-biztalk-scenario/"
---

## Background

When you’re building BizTalk applications with [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) Build and then doing some deployment you pretty much end up with the situation where you DO NOT want to create BizTalk applications manually or automatically from some text-file. There is actually a place where this information is stored and it’s called a Bindings File.

A Binding File contains information specific for a BizTalk application and it’s in XML. If you really are into details I encourage you to read [Structure of a Binding File](<http://msdn.microsoft.com/en-us/library/aa559878(BTS.70).aspx>) article on MSDN. In short you should use Binding Files and only Binding Files as a part of your automatic deployment process. Thanks to [Johan Hedberg](http://blogical.se/blogs/johan/default.aspx) and [Mikael Håkansson](http://blogical.se/blogs/mikael/default.aspx) for pointing this out.

Hey this has nothing to do with Moles! Be patient and start by installing Moles here are the free download links from the [Visual Studio](http://www.microsoft.com/visualstudio/en-us) Code Gallery:

- x64 version of Moles [http://visualstudiogallery.msdn.microsoft.com/22c07bda-ffc9-479a-9766-bfd6ccacabd4/](http://visualstudiogallery.msdn.microsoft.com/22c07bda-ffc9-479a-9766-bfd6ccacabd4/)
- x86 version of Moles [http://visualstudiogallery.msdn.microsoft.com/b3b41648-1c21-471f-a2b0-f76d8fb932ee/](http://visualstudiogallery.msdn.microsoft.com/b3b41648-1c21-471f-a2b0-f76d8fb932ee/)

## Moles

> _**Moles**_ is a lightweight framework for _**test stubs and detours in .NET**_ that is based on delegates. Moles may be used to detour any .NET method, including non-virtual/static methods in sealed types. _**Moles is freely available on Visual Studio Gallery**_ or bundled with [Pex](http://research.microsoft.com/pex).

That’s what [Microsoft](http://www.microsoft.com/en-us/default.aspx) Research introduces Moles as so now back to my real life scenario.

## Writing extensions to the BindingInfo class with Moles

So there is actually a class in the assembly _**Microsoft.Biztalk.Deployment.dll**_ that wraps the structure of a Binding File into a .Net class. So my thought was to write some helpful extensions methods that could help me in retrieving the name of the BizTalk application configured in the Binding File.

Are you still with me?

If we take a closer look at the _**BindingInfo**_ class:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb13.png "image")

The _**BindingInfo**_ has a default constructor that takes no parameters and then there is a LoadXml method that takes a string to the path of the Binding File. So now I have several options:

- I could create my own implementation of _**BindingInfo**_ and create a subclass that I could mock
- I could use any other Mocking framework, like [moq](http://code.google.com/p/moq/)
- I could use strictly integration tests (tests that use external resources) to test my extension methods

In this case I decided to use Moles because I like to try out new stuff. (Mind you this is not the most common scenario on when to use Moles but just bare with me)

Have you installed it yet? Good lets move on.

### Adding a reference to a Moled assembly

Right click any referenced assembly and choose Add Moles Assembly like so:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb14.png "image")

Rebuild your test project and you’ll find two new items added to your project.

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb15.png "image")

The first new item is a reference to an assembly that is the Moles representation of (in this case) _**Microsoft.BizTalk.Deployment.Moles**_.

And the second new item is a .moles file is a simple XML file telling Moles which Moles representation should be created at build time. This is how my _**Microsoft.BizTalk.Deployment.moles**_ looks like:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb16.png "image")

This can have implications on build performance that I will try to write another post about soon.

Now you have everything in place to start writing your tests right? Let’s goooo! First of all you’ll need to decorate all your test methods where you’re going to use Moled objects with the **[HostType](<http://msdn.microsoft.com/query/dev10.query?appId=Dev10IDEF1&l=EN-US&k=k(MICROSOFT.VISUALSTUDIO.TESTTOOLS.UNITTESTING.HOSTTYPEATTRIBUTE);k(TargetFrameworkMoniker-%22.NETFRAMEWORK%2cVERSION%3dV4.0%22);k(DevLang-CSHARP)&rd=true> "HostType")** attribute. I use my own code snippet for [this]({{site.baseurl}}/assets/other/testmethodmoles.snippet.zip) (based on the **[TestMethod](<http://msdn.microsoft.com/query/dev10.query?appId=Dev10IDEF1&l=EN-US&k=k(MICROSOFT.VISUALSTUDIO.TESTTOOLS.UNITTESTING.TESTMETHODATTRIBUTE);k(TargetFrameworkMoniker-%22.NETFRAMEWORK%2cVERSION%3dV4.0%22);k(DevLang-CSHARP)&rd=true> "TestMethod")** code snippet already in Visual Studio).

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb17.png "image")

And then start writing test cases. But how do you find your Moled objects? Well I usually find my Molable object using the **_complete namespace to the stuff you want to Mole + Moles + M + intellisense_**. Look at the example below:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb18.png "image")

If you use **_complete namespace to the stuff you want to Mole + Moles + S + intellisense_** then you’ll get the stubbable instances instead like so:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb19.png "image")

In my scenario I wanted to make sure that when the _**LoadXml**_ method is called on any _**BindingInfo**_ object in my tests it doesn’t read stuff from my disk but it just returns or initializes any properties I’m interested in. This is how I would write this:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb20.png "image")

A quick recap:

- _**MBidingInfo**_ was found using the **_complete namespace to the stuff you want to Mole + Moles + M + intellisense_** method mentioned above.
- _**AllInstances**_ means that for all instances I want to replace some method with my own delegate.
- _**LoadXmlString**_ is the method I want to Mole. After a while using Moles you’ll see that methods have there original name followed by the Type of the input parameters if any.
- Then I’ve written my delegate declaration and body that takes in two parameters called instance and path.

To make this even more interesting you can add Asserts in the delegate (I say you can, I’m not sure this is a good practice) like the following picture:

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb21.png "image")

Now I can continue writing my test as if my _**BindingInfo**_ objects had loaded the xml from file. But how can I verify that some Moled method has been called? Well Moles doesn’t intend to provide you with Mocking framework abilities that you might be used to. Instead you’ll have to do this yourself. Here is a simple example how to test if a method is called.

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb22.png "image")

So this was a real short introduction to Moles that I hope helps you come up to speed quickly. The final point that I would like to make is that you can use this on any .Net object even static or sealed objects like [**DirectoryInfo**](http://msdn.microsoft.com/en-us/library/system.io.directoryinfo) for instance (try using the code below without Moles):

![image]({{ site.baseurl }}/assets/images/2011/09/image_thumb23.png "image")

Big thanks to my colleague [Marcus Hammarberg](http://www.marcusoft.net) for challenging me to write this post!

Enjoy,

Hugo

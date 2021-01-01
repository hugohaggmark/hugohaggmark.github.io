---
layout: post
title: How Do I Do Moles?
date: 2012-01-04 13:05:00.000000000 +01:00
tags:
  - BDD
  - Moles
  - TDD
permalink: "/2012/01/04/how-do-i-do-moles/"
---

### Background

I’ve just recently started my first Open Source project (there I said it now I definitely have to deliver it). I can go so far to say that it has something to do with [TFS](http://msdn.microsoft.com/en-us/vstudio/ff637362) 2010 and a [Visual Studio](http://www.microsoft.com/visualstudio/en-us) package. I got you curious now right! Anyway there are some challenges to be solved when integrating your own code with existing APIs like the TFS Client API, it’s hard to unit test right? Well some would argue that you shouldn’t care to test your interaction with other APIs but for me that’s not an option. So I’ve chosen 2 basic strategies:

1. Wrap the TFS Client API with my own classes and Interfaces.
2. Test my own classes with the help from ["Pex and Moles - Isolation and White box Unit Testing for .NET"](http://research.microsoft.com/en-us/projects/pex/). Moles is a Mocking framework from [Microsoft](http://www.microsoft.com/en-us/default.aspx "Microsoft") Research.

I’ve written about my experiences with Moles in ["How I used Moles in a real life BizTalk scenario"]({{ site.baseurl }}{% link _posts/2011-09-12-how-i-used-moles-in-a-real-life-biztalk-scenario.md %}) and you should really read that post if you’ve never used Moles before and want to continue reading this post.

### A simple wrapper class

Lets start off with a very simple wrapper class that enables you to connect to a TFS 2010 instance.

```cs
using System;
using Microsoft.TeamFoundation.Client;
using Microsoft.TeamFoundation.Framework.Common;

namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles
{
  public class TfsConnectionWrapper
  {
    private TfsTeamProjectCollection tfsTpc;
    public TfsConnectionWrapper()
    {
    }
    public void Connect(string url)
    {
      tfsTpc = TfsTeamProjectCollectionFactory.GetTeamProjectCollection(new Uri(url));
      tfsTpc.Connect(ConnectOptions.None);
      tfsTpc.EnsureAuthenticated();
    }
  }
}
```

As you can see this wrapper class uses some TFS Client API like the call to _**TfsTeamProjectCollectionFactory.GetTeamProjectCollection**_ for instance. Well if we take a close look at _**TfsTeamProjectCollectionFactory**_ we’ll find the following declaration:

```cs
using System;
namespace Microsoft.TeamFoundation.Client
{
  public static class TfsTeamProjectCollectionFactory{}
}
```

As you can clearly see _**TfsTeamProjectCollectionFactory**_ is a static class with now chance of sub classing or extending to enables some sort of mocking. So this is really where a mocking framework like Moles comes to play.

Before we start looking at any unit test code I must confess that I’ve started to categorize all my unit tests that use Moles with a test category called MolesTest. I started using the

```cs
[TestCategory("MolesTest")]
```

notation at first but then I just created my own TestCategoryAttribute like this:

```cs
using System.Collections.Generic;
using Microsoft.VisualStudio.TestTools.UnitTesting;
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  public sealed class MolesTestAttribute : TestCategoryBaseAttribute
  {
    public override IList<string> TestCategories
    {
      get { return new[] { "Moles" }; }
    }
  }
}
```

Doing this enables me to just add MolesTest next to all my TestMethod attributes like so which makes it easy to filter Moles tests if necessary.

```cs
[TestMethod, MolesTest]
```

### How I used to do Moles

The following example illustrates how I used to use Moles as part of my unit testing. (by the way looking at this code now makes my body shiver with disgust)

```cs
using System;
using System.Net;
using Microsoft.TeamFoundation.Client.Moles;
using Xunit;
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  [Microsoft.VisualStudio.TestTools.UnitTesting.TestClass]
  public class TfsConnectionWrapperTests
  {
    private const string validUrl = "http://valid";
    [Microsoft.VisualStudio.TestTools.UnitTesting.TestMethod, MolesTest]
    [Microsoft.VisualStudio.TestTools.UnitTesting.HostType("Moles")]
    public void using_an_valid_url_string_should_not_throw_the_old_way_I_used_to_do_moles()
    {
      Assert.DoesNotThrow(() =>
      {
        MTfsTeamProjectCollection mole = new MTfsTeamProjectCollection();
        MTfsConnection.AllInstances.ConnectConnectOptions = (connection, options) => { };
        MTfsConnection.AllInstances.EnsureAuthenticated = (connection) => { };
        MTfsTeamProjectCollectionFactory.GetTeamProjectCollectionUri = (uri) =>
        {
          if (uri.Equals(new Uri(validUrl))) return mole;
          throw new WebException();
        };

        TfsConnectionWrapper wrapper = new TfsConnectionWrapper();
        wrapper.Connect(validUrl);
      });
    }
  }
}
```

Let’s go through a couple of things before I completely throw this in the recycle bin for ever:

1. Yes I’m using [xUnit.net](http://xunit.codeplex.com/ "xUnit.net") as my preferred Assert framework, because I think it’s more descriptive then the default in Visual Studio and it makes the transition to xUnit.net in the future simpler. And because of that I need to prefix all my attributes with Microsoft.VisualStudio.TestTools.UnitTesting. So why not use xUnit all the way? Well that’s a whole other future blog post to be written. Let’s stick with fact that the combination xUnit, Moles and TFS Build and getting stuff published back to TFS caused me some serious headache in the past and that deserves a place in an other post.
2. I’m not going to bother right now verifying the input to the Moles delegates except for the MTfsTeamProjectCollectionFactory.GetTeamProjectCollectionUri delegate where I want to make sure that any invalid uri throws.
3. This would be my starting point then I would probably write another test method to make sure that passing invalid url would indeed throw an exception. In creating that second method I would of course refactor all my moles setups into separate methods. The end result would look something like so:

```cs
using System;
using System.Net;
using Microsoft.TeamFoundation.Client.Moles;
using Xunit;
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  [Microsoft.VisualStudio.TestTools.UnitTesting.TestClass]
  public class TfsConnectionWrapperTests
  {
    private const string validUrl = "http://valid";
    [Microsoft.VisualStudio.TestTools.UnitTesting.TestMethod, MolesTest]
    [Microsoft.VisualStudio.TestTools.UnitTesting.HostType("Moles")]
    public void using_an_valid_url_string_should_not_throw_the_old_way_I_used_to_do_moles()
    {
      Assert.DoesNotThrow(() =>
      {
        SetupAllMoles();
        TfsConnectionWrapper wrapper = new TfsConnectionWrapper();
        wrapper.Connect(validUrl);
      });
    }

    [Microsoft.VisualStudio.TestTools.UnitTesting.TestMethod, MolesTest]
    [Microsoft.VisualStudio.TestTools.UnitTesting.HostType("Moles")]
    public void using_an_invalid_url_string_should_throw_the_old_way_I_used_to_do_moles()
    {
      Assert.Throws <WebException>(() =>
      {
        SetupAllMoles();
        TfsConnectionWrapper wrapper = new TfsConnectionWrapper();
        wrapper.Connect("http://invalidurl");
      });
    }

    private static void SetupAllMoles()
    {
      SetupConnectMole();
      SetupEnsureAuthenticatedMole();
      SetupGetTeamProjectCollectionUriMole();
    }

    private static void SetupConnectMole()
    {
      MTfsConnection.AllInstances.ConnectConnectOptions = (connection, options) => { };
    }

    private static void SetupEnsureAuthenticatedMole()
    {
      MTfsConnection.AllInstances.EnsureAuthenticated = (connection) => { };
    }

    private static void SetupGetTeamProjectCollectionUriMole()
    {
      MTfsTeamProjectCollection mole = CreateTeamProjectCollectionMole();
      MTfsTeamProjectCollectionFactory.GetTeamProjectCollectionUri = (uri) =>
      {
        if (uri.Equals(new Uri(validUrl)))
        return mole;
        throw new WebException();
      };
    }

    private static MTfsTeamProjectCollection CreateTeamProjectCollectionMole()
    {
      return new MTfsTeamProjectCollection();
    }
  }
}
```

So in the past I would feel reasonably happy with this and then move on with the next test case. But then I would come to a point that other test classes needed the same Moles setup and then I would create a class that all test classes derived from. Phuu…there had to be a better approach and as I never settle and always seek ways to improve my skills and code I’ve now reached an approach that for the moment feels comfortable.

### Introducing Testable Moles classes

Nowadays I just create base classes that derive from (if possible) a Moles class. This way I can define my default behavior for my Testable Moles class and reuse it in any other test classes. And by marking the methods in the class used in the Moles delegates as virtual I can create different Testable Mole classes. Clever right? Let’s take a look at my implementation of a base class for a TestableTfsConnectionBase:

```cs
using Microsoft.TeamFoundation.Client;
using Microsoft.TeamFoundation.Client.Moles;
using Microsoft.TeamFoundation.Framework.Common;
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  internal abstract class TestableTfsConnectionBase : MTfsConnection
  {
    protected TestableTfsConnectionBase(): base(new MTfsTeamProjectCollection())
    {
      InitConnectMole();
      InitEnsureAuthenticatedMole();
    }

    private void InitEnsureAuthenticatedMole()
    {
      AllInstances.EnsureAuthenticated = (connection) =>
      {
        SetupEnsureAuthenticatedMole(connection);
      };
    }

    private void InitConnectMole()
    {
      AllInstances.ConnectConnectOptions = (connection, options) =>
      {
        SetupConnectMole(connection, options);
      };
    }

    protected virtual void SetupConnectMole(TfsConnection connection, ConnectOptions options)
    {
    }

    protected virtual void SetupEnsureAuthenticatedMole(TfsConnection connection)
    {
    }
  }
}
```

After this I would just go ahead and create a “default” test class

```cs
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  internal class TestableTfsConnection : TestableTfsConnectionBase
  {
  }
}
```

and if I need I can create another class that implements another behavior like the following class that always throws when trying to connect:

```cs
using System;
using Microsoft.TeamFoundation.Client;
using Microsoft.TeamFoundation.Framework.Common;
namespace HugoHaggmark.Blog.Moles.HowDoIDoMoles.Tests
{
  internal class TfsConnectionThatThrowsOnConnect : TestableTfsConnectionBase
  {
    protected override void SetupConnectMole(TfsConnection connection, ConnectOptions options)
    {
      throw new DivideByZeroException();
    }
  }
}
```

I hope you get the picture on how the testable classes works. Now let’s return to our unit test class and see how this would look in practice:

```cs
[Microsoft.VisualStudio.TestTools.UnitTesting.TestMethod, MolesTest]
[Microsoft.VisualStudio.TestTools.UnitTesting.HostType("Moles")]
public void using_a_connection_that_always_throws_should_throw_the_new_way_I_use_moles()
{
 Assert.Throws<DivideByZeroException>(() =>
 {
  MolesFactory.SetupMole<TfsConnectionThatThrowsOnConnect>();
  MolesFactory.SetupMole<TestableTfsTeamProjectCollection>();
  MolesFactory.SetupMole<TfsTeamProjectCollectionFactoryThatNeverThrows>();
  TfsConnectionWrapper wrapper = new TfsConnectionWrapper();
  wrapper.Connect("http://invalidurl");
 });
```

As you can see any new test method would just simply initialize any testable mole class needed and then of you go. I made a small class called _**MolesFactory**_ that simply creates an instance of the supplied type in the SetupMole just because I believe it makes my code more descriptive and readable. The complete solution with code and binaries can be downloaded from [here]({{ site.baseurl}}/assets/other/HugoHaggmark.Blog.Moles.HowDoIDoMoles.zip).

This is the first time I reveal my inner thoughts and code approaches so I really hope you got a clear understanding and some inspiration. Really looking forward to get feedback from the Moles community regarding the approaches described in this post.

Until next time,

Hugo

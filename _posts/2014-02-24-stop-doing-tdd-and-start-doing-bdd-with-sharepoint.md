---
layout: post
title: Stop doing TDD and start doing BDD with SharePoint!
date: 2014-02-24 12:00:10.000000000 +01:00
tags:
  - BDD
  - Fakes
  - Moles
  - SharePoint
  - TDD
permalink: "/2014/02/24/stop-doing-tdd-and-start-doing-bdd-with-sharepoint/"
---

I’m certainly one of those guys that like to do TDD and If you ask people what differentiates me from other developers you will almost certainly get the answer that I'm really serious about quality.

But after my last project I’ve been become “wiser” or more careful with what kind of tests to use when and where. So why the unusually provoking title?

Last week I got a tweet from my friend [Johan](http://johanleino.wordpress.com/) about tips on how to do TDD in SharePoint with assemblies that reside in the GAC. ![Stop doing TDD and start doing BDD with SharePoint]({{ site.baseurl }}/assets/images/2014/02/image.png)

For a brief second I thought that I would just give him a list with the different methods/hacks/workarounds I’ve seen and then I thought:

> “you’re only fighting the symptoms, we deserve better"

SharePoint lacks good mockable Interfaces and serves you with lots of sealed classes and static classes. That’s not a great foundation for TDD.

Furthermore we have the whole GAC thingy, also not a great foundation for TDD. If only I would have received a euro for every time someone in my last project asked me why the tests were not passing and whereby I asked “do you have an older assembly in the GAC?”

I can’t see how trying to do TDD in SharePoint will lead to anything but workarounds or hacks to get it working.

Another aspect of truly doing TDD is that you really need a vast knowledge of the domain and IMHO very few developers doing SharePoint development have the necessary knowledge.

In fact I did a test with two groups in my last SharePoint project. Both groups were told to TDD a small Document Set feature. Both groups were given 1½ hour. I coached the second team and 5 min into the exercise I told my group that let’s skip TDD and just do the feature.

The result was spectacular as you might have guessed. The first group almost didn’t make any progress at all and the second group didn’t come very far either. Why? Not enough knowledge of the domain and no mockable interface makes it almost impossible to do TDD in my opinion.

Still not convinced? Well it’s a free world go ahead, try it, fail and become wiser yourself.

I’ll even give you some methods I’ve used/seen/hacked/worked around the last couple of years (without any specific order of importance):

1. Set Assembly version to 1.0.0.\*, this way you won’t have any issues with the GAC thingy. But you’ll have lots of other side effects that you need to mitigate/automate.
2. Retract dll’s in test setup/teardown.
3. Write you class under test in the test project and then move it. [Johan wrote about a great spin-off on this method in Workaround for unit testing code that reside in the GAC](http://johanleino.wordpress.com/2014/02/20/workaround-for-unit-testing-code-that-reside-in-the-gac/).
4. Wrap SharePoint artifacts with your own Interfaces.
5. Separate your domain from the infrastructure, and TDD the domain object only leave the infrastructure alone!
6. Use a mocking technology like Fakes/Shims/Moles/TypeMock. This demands a really deep knowledge and understanding of SharePoint so if you think this is something you’d like to try, I say beware you’re treading in deep dark waters.

What you should do instead is to treat SharePoint as the framework it is much like the way you would treat the .Net Framework.

BDD is a nice way to do this, you invest more time in designing behaviors or capabilities of your application using SharePoint as one of those capabilities.

But that's just my very personal 2 cents,

Hugo

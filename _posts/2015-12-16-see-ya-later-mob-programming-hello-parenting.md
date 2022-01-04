---
title: See ya later Mob programming, hello parenting
date: 2015-12-16 08:00:20.000000000 +01:00
tags:
  - Personal
permalink: "/2015/12/16/see-ya-later-mob-programming-hello-parenting/"
---

![Vader and Daughter From the Book Vader's little Princess]({{ site.baseurl }}/assets/images/2015/12/Vader-and-Daughter-06-300x292.jpg)

Yes you heard me right! I'm leaving my work life for a fulltime life as a father for our daughter Paula, beginning the 23rd of December.

I'll be on my parental leave until August/September 2016 and I'm excited and really looking forward to it.

I’ve come up with some small tasks/goals that I would love to accomplish during my parental leave.

These are tasks/goals aside from parenting that will be my absolute focus, that goes without saying.

Surely I'll never finish or even start these tasks/goals but here goes anyways:

- Complete Fallout 4, hehehe good luck with this one Mr. Häggmark
- Paint the south side of the house, let's procrastinate this one until it gets warmer
- Move blog from WordPress to GitHub pages, this is just a technical journey and I think I can learn lot's from it
- Create an Android watch face where the background is a slideshow from Google Photos, well can't find any good watch face that does this except OneDrive and it has stopped working for some reason.

There you go, I'm all setup for my parental leave but as a last thing a thought I'd blog about all the stuff I've learned during my last almost 2 years at TUI Nordic aka Fritidsresor.

If you're not interested in reading about keeping yourself lean, Agile and fast stop reading now. For the rest of you I hope you enjoy.

## Mob programming

Our team has been Mob programming for more than one year now. It's still fun and It's really obvious that we all are so Intune with each other that there is very little discussion on how to solve the basic stuff.

When new challenges arise though we have great discussions about how to best solve these challenges.

By the way I think this might be one of the Achilles heels for Mob programming, after a long while you really need to bring in new people in the mob or your mob will be too tight and unified.

See my previous post [here]({{ site.baseurl }}{% link _posts/2015-06-04-mob-programming-in-short.md %})for more info.

## Outsourcing

While we have made the transition from an agile team to a really agile team thanks to Mob programming, some parts of the organization here has gone the exact opposite direction.

Let’s call the “others” the Empire not because they were evil in any way but because they were large, very hierarchicaly organized and headed straight for their doom.

Our team on the other hand worked like true Rebels (and I’m Chewbacca of course). Challenging the norm that was forming and working fast, small and fighting alongside the huge monster that the Empire had become.

By building an organization that was mostly outsourced, with many different hierarchy layers with release managers and configuration managers the Empire had really set them self-up for trouble with a non-agile work environment with long release cycles and long deployments (yes we're talking many hours to deploy a release).

At several points, we the Rebels, had to sweep in and rescue the slow Empire because the really didn’t manage to release stuff in time and business was hurting.

There is even a story told that one of the teams in the Empire thought that our Rebel team had 40 (we were 5 at the time) team members because we were going so fast.

What I think is really fascinating is that people working for the Empire thought they're doing all the right things. In contrast we thought we were doing all the right things before we started to Mob program.

Now we know better.

## MVP

Another approach that we use in the Rebel team is the concept of MVP. That means that every time a PO (product owner) comes to us with a new task/user story/requirement/whatever you call it/ they will have to argue their case real good to get 100% of their task released out to production.

We often persuade the PO’s that the essence of the task can be released first and then if that works out we can look at the rest. Sometimes “the rest” will never see the daylight of production.

This way of working together with the PO keeps our tasks small and fast.

## Less Estimates

We work almost 99% without any planning or time estimates. We work with a continuous stream of work that never ends.

Sometimes a PO will approach us with a task that isn’t an MVP and that she/he want to know how long it will take. Seriously this isn’t a very strange question in fact it’s surely a common question within the Empire for instance.

First of all we try to break things into MVPs (read above) and secondly we try to turn the question to the PO.

Rebels:

-When do you need this “thing” in production?

PO:

-I want it at this date x.

Rebels:

-Well that’s not possible but we’ll have this MVP of your task on this X date, good enough?

PO:

-Yes

By turning the question to the PO they will have to engage in the decision and we find that it’s easier to continue the discussion.

Only on 2 occasions during these last 2 years we had to estimate the old fashion way.

Both times that has led to an estimate that was more than 6-8 months which is somewhat insane if you think about it.

## Micro Services

Another technique/practice that we use is to build Micro Services. Mind you we don’t have a “written in stone” kind of law that determines the size of our Micro Services.

Instead we use our collective Mob mindset to create smallish components that do one thing great (except for 1-2 Micro Services that do more than one thing). With this comes the included benefit of not writing unit tests. No tests you read, I can see the horror in your eyes. Wait for it...

We write end-to-end tests for the Micro Services and unit tests for very complex rules (there are few in our domain). With the end-to-end type of approach we can verify that the complete (but small) Micro Service is working as expected. Furthermore for some of the absolute smallest Micro Services we skip the tests completely.

## Topshelf

The majority of our Micro Services are backend services and they do stuff like this:

1. Read stuff from an external source like a database table or flatfile
2. Transform stuff to a json structure
3. Store json and publish json on event queue

To make this deployable and runnable on Windows we’ve taken the approach to create a Windows Service for every backend Micro Service. This can be a hassle if you use the templates provided in Visual Studio (I sure have had a lot of irritation with them) to create a Windows Service.

Instead we use the TopShelf which makes creating and debugging Windows Services like a walk in the park.

## RabbitMQ

Some of our Micro Services are used by other teams and they like to be notified when changes occur. For this we use RabbitMQ as a mechanism for event based message system.

RabbitMQ is easy to get started with, does the job and it’s free.

## Nancy

Our “frontend” Micro Services mostly do this:

1. Answer some GET route via http
2. Read json stored from backend Micro Service
3. Return json to requester

For this we use the lightweight web framework called Nancy. It’s the best in the .Net world because it so easy and lightweight and nonintrusive.

We could use used Node.js but then we would have to change our deployment mechanism as well.

## Octopus Deploy

Speaking of the deployment mechanism, we use Octopus Deploy for all our deployments. And it’s very nice for .Net deployments and has a good API that we call from our builds.

The only drawback is the use of PowerShell within Octopus but that’s whole other rant that I don’t want to get into now.

## GitHub

We use git as our source control and we host all code on GitHub which has been great. Easy to use and good documentation.

## Jenkins

We use Jenkins as our build server mainly because it’s free. I believe there’s better build servers but Jenkins does the job nicely and has many many plugins.

## Hubot

Lately we have automated some of our daily jobs like:

- Create a repo on GitHub
- Create a job in Jenkins
- Create a project in Octopus
- Create a deployment pipeline for a backend Micro Service called Foo (creates repo + Jenkins job + Octopus project base on a backend project and connects it all)
- Create a deployment pipeline for a frontend Micro Service called Bar (creates repo + Jenkins job + Octopus project base on a frontend project and connects it all)

We do this by typing a specific message in our chat channel (we use Slack). That message is then picked up by our Hubot bot that executes the commands.

## Slack

Slack is our preferred communication channel because it’s simple and has many connectors like GitHub and the one for Hubot.

## Conclusion

There you have it, some of the techniques/technologies that I’ve learned and adapted during the 2 years I’ve been at Fritidsresor. These techniques/technologies are what keeps us fast, lean and small like the Rebels we are.

Looking back to before this assignment at Fritidsresor I’ve worked as consultant in large enterprise projects and I’ve always felt that there was so much potential being lost during those projects. That’s when it hit me like an iron fist in my face.

I too have been part of the Empire.

But somehow this conflict inside me during my time in the Empire has made me choose another side; a lighter side.

Many thanks go out to the ones still fighting fight, you know who you are. Don't give into hierarchies, estimates or release managers because that leads to the dark side.

May the fork be with you,

Chewbacca

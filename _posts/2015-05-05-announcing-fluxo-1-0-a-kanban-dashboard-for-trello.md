---
layout: post
title: Announcing Fluxo 1.0, a Kanban Dashboard for Trello
date: 2015-05-05 07:16:00.000000000 +02:00
categories:
  - Personal
tags:
  - DashBoards
  - Fluxo
  - Kanban
  - Personal
  - Trello
permalink: "/2015/05/05/announcing-fluxo-1-0-a-kanban-dashboard-for-trello/"
---

&nbsp;

Now that’s a great milestone Hugo but can you tell me what’s new in this release?

-Almost&nbsp;nothing, but let’s turn the clock back a week…

About a week ago on my way to work I started to notice how my Kanban dashboard had some trouble loading. Naturally I opened up the developer tools in Chrome and noticed a lot of “429 Too Many Requests” errors. (The Trello api has a Rate Limit that you can read about here [http://help.trello.com/article/838-api-rate-limits](http://help.trello.com/article/838-api-rate-limits) )

That’s odd, it can’t be that many people using Fluxo at this time in the morning I thought to myself, and then it hit me. Someone had probably hijacked my Trello application key because I was using Trello’s own Client.js with the key and it was all stored on GitHub in a public repo.

From Trello’s on how to use their Client.js documentation:

```
**\<head\>** _\<!-- ...&nbsp; --\>__\<!-- The client library requires jQuery&nbsp; --\>_ **\<script** src="http://code.jquery.com/jquery-1.7.1.min.js" **\>\</script\>**** \<script **src="https://api.trello.com/1/client.js?key=substitutewithyourapplicationkey"** \>\</script\> **_\<!-- ...&nbsp; --\>_** \</head\>**
```

I remember thinking that this client side key exposure could cause some issues the first time I started to use it but I hadn't thought about it ever since.

Anyways I decided to do a complete rewrite and use a server side OAuth solution instead. And last Friday I actually finished it.

To sum it up Fluxo now uses a server side OAuth solution and I also added a 5 minute caching on all requests to prevent hitting the Rate Limit of 300 requests per 10 seconds.

This time no keys are stored in the repo, a lesson I’ve learned the hard but funny way,

Hugo

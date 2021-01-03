---
layout: post
title: End to end TFSBuild pattern for BizTalk 2009, part 3 (Commitment)
date: 2010-10-06 19:26:02.000000000 +02:00
tags:
  - BizTalk
  - Recommended Practices
  - Team Foundation Server
permalink: "/2010/10/06/end-to-end-tfsbuild-pattern-for-biztalk-2009-part-3-commitment/"
---

So you thought that I would give away the secrets this time huh? I'm just kidding it will all be revealed soon in the last part of these series.

Anyway, how come I make this artistic pause? Anyone? Let's begin with some basics:

- Automated builds or continuous integration is useless if the team using it isn't committed to solving problems with the builds and/or maintaining the builds.
- Visualize the status of your builds on digital boards, flat screens or with emergency lights/sounds and make sure everyone in the project knows the status of the builds.
- When **all** builds are green and **all** tests are green in your project you are in what I call a **normal** stage. In a **normal** stage you should make time for small improvements throughout your team i.e. refactoring, code reviews etc.
- If **any** builds are red or **any** tests are red in your project you are in what I would call a **deviate** stage. In a **deviate** stage you should really work as a team to solve the deviations and most importantly **learn** from it and finally make **improvements** in how your team works.

Is your team committed to these basics? If not take a good look at the benefits of automated builds:

- Always the same automated way of compiling, building and deploying your codebase.
- No more manual releases.
- One single location for the compiled binaries i.e. the drop folder. Make sure that all deployment flows from the drop folder i.e. establish **one** deployment method that uses the contents of the drop folder and use it on **every** environment starting with your own every morning.
- Automated regression tests.
- Intact source tree
- \<Enter a clever reason here\>

Still not convinced? Well then you should really visit a dev shop where they're committed to this and compare their speed, quality, agility and team spirit to your own; and I'm sure you will get inspired.

Au revoir

Hugo

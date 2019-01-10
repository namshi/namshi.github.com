---
layout: post
title: "7 (long) years later: how long it took to rewrite Namshi"
date: 2019-01-10 11:44
comments: true
categories: [namshi, general]
author: Alessandro Nadalin
---

I joined Namshi of the 1st of April (*what a joke!*) of 2012.
Fast forward to around 7 years later...

A couple weeks ago, I tried to login into one of our legacy
internal services here at Namshi: to my surprise, I was
redirected to a brand new, flashy app that seemed to have
replaced that good old monolith.

What does that mean?

<!-- more -->

It means multiple things: first and foremost, our team
rocks! They completely replaced one of our oldest services
without people (granted, *people like me*) noticing.

But the most important thing I realized was that was
the last (and oldest) service which was in use at the
time I joined Namshi, around 7 years ago.

That's it: our team managed to rewrite the whole of Namshi
over the past 7 years, a feat that reminds me of the [words
of Dave Hagler, systems engineer at AOL](http://highscalability.com/blog/2014/2/17/how-the-aolcom-architecture-evolved-to-99999-availability-8.html):

{% blockquote %}
The architecture for AOL.com is in it’s 5th generation.  It has essentially been rebuilt from scratch 5 times over two decades.  The current architecture was designed 6 years ago.  Pieces have been upgraded and new components have been added along the way, but the overall design remains largely intact.  The code, tools, development and deployment processes are highly tuned over 6 years of continual improvement, making the AOL.com architecture battle tested and very stable.
{% endblockquote %}

We didn't really start our "SOA" mission until 2013, when
it became clear that our 2 monoliths (frontend & backend)
wouldn't be able to help as much while we were trying to
scale in a lean way: we first started building APIs for our
catalog, checkout, order processing...   ...until late
December 2018, when the last service (codename `bob`)
was decommissioned.

We've come a long way, I must admit it. What we achieved
over almost a decade here makes me proud of being one of
the earliest Namshees.

I want to thank Ala and [Sakina](/team/#Sakina%20Sagarwala) for rewriting `bob`, now
only a memory, as well as [Razan](/team/#Razan%20Bilwani) and [Ayham](/team/#Ayham%20Alzoubi) who are the
only other members of our tech team I had the pleasure
to work together with since the start of my adventure here.

What a ride, folks!
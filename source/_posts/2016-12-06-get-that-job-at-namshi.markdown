---
layout: post
title: "Get that job at Namshi"
date: 2016-12-06 10:54
comments: true
categories: [job, interview]
author: Alessandro Nadalin
---

Inspired by [HauteLook](https://hautelook.github.io/jekyll/update/2015/04/01/get-that-job-at-hautelook.html) who,
in turn, were inspired by [Steve Yegge](http://steve-yegge.blogspot.ae/2008/03/get-that-job-at-google.html),
I decided to write a small article that briefly describes what we're looking for
when interviewing potential candidates.

<!-- more -->

## The process

Our recruitment process tends to be quite lean (with a few exceptions :)):

* a first, introductory chat with me to get to know each other (*45m*)
* a chat with someone from our HR department, so that the candidate gets a solid overview on our company, the culture, benefits and so on. This is also very helpful for people who are relocating, as it's the best time to ask anything about working in Dubai and so on (*1h*)
* a more technical interview with someone from our [tech team](/team) (usually 2 of our senior engineers) (*1/2h*)

Additionally, depending on the candidate and the position, there **might** be a
few additional steps required:

* coding challenge (*no deadlines, should take up to 2 hours of your time*)
* additional technical screening with me (*1h*)
* chat with one of our managing directors (*45m*)

So you can generally assume that, after 3 positive interviews (3/4 hours in total), you
could theoretically receive an offer letter.

Expect the whole process to take around 3 weeks.

## Backend

You should be familiar with minimalist frameworks like Express or Silex, as that's
how we build 99% of our services nowadays. We're not big on any particular language,
but if you worked with Node that's definitely a plus, as well as understanding
async programming.

Being familiar with a shell is kind of a must, as we want people who can poke
around with Linux and are aware of the potential of the "*do one thing and do it well*"
philosophy.

The HTTP protocol (both 1.1 and 2) is another must as that's what we speak each
and every day -- you will be mainly tasked to write applications that talk to
other services through HTTP. Knowing what changes with HTTP/2 and why that's
great shows the kind of awareness we're looking for.

We're not big on algorithms and data structures but that doesn't mean you
shouldn't be able to understand them -- having some basic knowledge of Big O
is always appreciated, as well as understanding how to pick a data structure in
order to make the most out of it.

We use both relational and non-relational databases, and you should be
comfortable with a few names here (mostly MySQL and Redis). On MySQL, questions
about race conditions, locking and `ALTER TABLE` might come up.

## Frontend

This position is all about JS -- forget CSS, forget HTML: 99% of this position
will mean JavaScript.

Callbacks? Promises? async / await? Breakfast for you :)

You should be familiar with technologies like Angular 1 and React, and how they
work behind the curtain. We could ask you to write a simpler, smaller version of
redux so you'd better understand how these libraries work.
For example, knowing that virtual DOM makes things faster won't cut it -- why,
how and thanks to what data structure will.

We also have a keen eye on performance, and you should too: understanding what
changes HTTP/2 brings to frontend engineering is a must, as well as knowing basic
rules for performance optimization (`webpack -p` anyone?). We aren't super-fussy
about the more advanced stuff, but if you mention tree-shaking and friends we
won't mind :)

You should be familiar with methodologies such as BEM as you will be required to
discuss what's the best approach to structure our styles for the long run.
We also like to sometimes use CSS animations, so knowing how to make them perform
better, especially for mobile browsers, is highly appreciated.

## Mobile

As a Mobile Engineer, we expect that you have full proficiency in developing apps 
for Android and/or iOS platform i.e from creating a new project in 
Xcode / Android studio to the publishing of app in Google Play and App Store.

We expect that you have  good knowledge about Java (If you have Android experience) 
or Objective C / Swift (If you have iOS experience). If you know all of these 
languages, most likely you are the rockstar we want to hire :) . We recently 
introduced Kotlin and React-Native in our apps, if you have hands-on 
experience on these, its surely a good bonus !!

And last but not the least.  Namshi apps are known for their blazing fast 
responsive UI, stability and the features that every online shopper 
wish to have. To take it to the next level, we use lot of C.I tools, 
automated builds, Build Optimization for stores, Automatic Distribution to testers. 
Analytics, Crash Reporting etc etc.  We expect that you are familiar 
with the tools and techniques to support these things.

## SRE

You should be very familiar with AWS or similar providers ie. Google Cloud, and
have some sort of experience with containers and orchestrators --
we use Kubernetes but if you worked with Mesos / Swarm we won't really mind.
A good answer to "*Why would you want to use an init system inside a container?*"
will definitely speed up the hiring process :)

Be big on Linux, as we expect you to be a ninja there -- utilities
like `awk`, `sed` and so on should be music to your ears.

You should be familiar with one scripting language (make it python, ruby, php, bash)
and be open to jump into code, as you might be required to provide bugfixes on
some of our services, or implement system-related features. In general, the more
you can code the happier everyone is!

A keen eye on security is a big plus -- we're a small team and we need brilliant
people who are going to take into account the problems that might happen when
using (or **not using**) a particular technology, pattern or methodology.

We are also big fans of automation and, in some sense, immutable infrastructures.
Know the basics.

## And we also generally look for...

* automated testing is king here: despite the fact that we don't try to cover
100% of the use-cases, we rely on automated tests a lot. Knowing how to write
them, and what methodologies to use to make tests more maintainable is a must.
* it would be great to see some of your code -- github is a fan-favorite here :)
* people who like [shawarma](https://en.wikipedia.org/wiki/Shawarma)

What are you waiting for? Drop everything and ~~order some shawarma~~ [apply now](/join-us/)!

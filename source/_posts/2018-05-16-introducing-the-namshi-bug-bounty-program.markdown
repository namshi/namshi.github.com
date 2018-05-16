---
layout: post
title: "Introducing the Namshi Bug Bounty Program"
date: 2018-05-16 08:00
comments: true
categories: [security]
author: Alessandro Nadalin
---

Namshi believes that working with skilled security researchers across the globe
is crucial in identifying weaknesses in any technology -- with that in mind, I
wanted to introduce our (currently private) *Bug Bounty Program* that's been in
place since a couple years.

<!-- more -->

## A bit of history...

5 (long) years ago we responded to our very first vulnerability report,
submitted by a web developer whose better half had been using our services,
who noticed a small glitch in one of our webservices. Since then, we processed quite a
few (and luckily *not-so-many*) submissions, handing off rewards to researchers who
would submit valid reports.

The process had been quite unstructured until a couple years back, when
[Boris](https://www.linkedin.com/in/borishajduk/) joined [GFG](https://global-fashion-group.com/),
at the time our majority stakeholder, and suggested we should try [hackerone](https://www.hackerone.com/)
as it had been working well for other companies -- needless to say, this was a
turning point for us, as we finally found a platform that could take care of
coordination with security researchers.

At that point we started phasing out the historical `security@namshi.com` email
address in favor of inviting researchers to our H1 program, which has definitely
helped us defining better boundaries (especially in terms of timeline, rewards and
scope of the program) between Namshi and the community of researchers out there.

## Our current program

As mentioned, we run a (private) program on hackerone and, in parallel, process
submissions to `security@namshi.com` by asking whoever reaches out to us to
create an account on hackerone so that we can then move the conversation from
email to a proper bug bounty platform.

Our program defines a disclosure policy, list of exclusions and a brief legal
appendix to guide you through the process of submitting a vulnerability report
to Namshi. The list of exclusion also contains an associated list of behaviors / actions
that will result in your submission being ineligible for a bounty, such as:

* making threats
* demanding payments / entry into the program in exchange for reports

...and a few additional points. We do believe our program is fair and guarantees
a good balance between what we demand and what we offer, but we're always open
to suggestions, or questions, from your side. Feel free to reach out if
you think we should amend some of the points in our program.

In addition, I wanted to mention that we recognize that the only public information
available on our websites ([our security FAQ](https://support.namshi.com/hc/en-us/articles/207782049-Security)),
is by no means exhaustive, and we plan on fixing it in the upcoming months:
that's where the next paragraph kicks in :)

## Future plans

You might be wondering: "why are you telling us about a private bug bounty program
that's been kept private and we don't know how to join? Is today the
lets-share-news-people-couldnt-care-less day?"

We're sharing this because we want this to change, and we want to be more open
about some of our processes: **our goal is to be able to make our program public in
the upcoming months**, so that more and more researchers can help us making Namshi
a safer place on the web.

The traditional challenges with having public bug bounty programs are related to
the "*signal vs noise*" ratio as well as the fact that companies think the more they
keep in the dark, the less they'll expose -- we don't share the same beliefs, and
are currently making a step to expand our program to more researchers, with the
ultimate goal of making it public. At the same time, our tech department is fairly small so we want
this transition to be as smooth as possible, hence the slow rollout -- consider
this a canary release until everything is well-oiled and we're comfortable enough
with making the program public.

With this in mind, I'd like to invite everyone who would like to take a look at
our program to mail us at `security@namshi.com` and share the email they use on
hackerone, so that we can invite you to the Namshi Bug Bounty program. As I
mentioned, this is a first step towards our program turning public in the upcoming
months.

Considering our goal to be more open and transparent, I would also like to take
a second to disclose some of our stats taken from hackerone:

* our **minimum bounty is $50**
* the total number of submissions is 67
* we have **resolved 27 reports** (meaning the remaining are to be considered invalid, or part of the exclusions)
* our average time to **first response is 1 day** (last 90d)
* our average time to **bounty is 3 days** (last 90d)
* our **average bounty range is $120 - $150**
* our **top bounty range is $450 - $1000**

Happy hacking!

---
layout: post
title: "Reflecting on microservices"
date: 2015-02-27 11:41
comments: true
categories: [soa, microservices, architecture]
author: Alessandro Nadalin
---

Yesterday we took some time off from work
to enjoy [Martin Fowler's take on microservices](https://www.youtube.com/watch?v=wgdBVIX9ifA),
so I wanted to recap what I really liked
about the video.

<!-- more -->

What I really appreciated about the talk is that
Martin reiterates on how [microservices aren't a free lunch](http://highscalability.com/blog/2014/4/8/microservices-not-a-free-lunch.html),
and I really liked the slide in which he specifically
mentions "*you must be this tall to use microservices*":
nowadays we have the right tooling to
[employ microservice-based infrastructures](http://nginx.com/blog/microservices-at-netflix-architectural-best-practices/)
but you really need to take advantage of that tooling
in order to be able to successfully deploy microservices.

A clear example is Docker: you might be ready to deploy a new
docker-based microservice in production but, if it takes a day
to provision new hardware, chances are that you will bang
your head against the desk on a daily basis since scaling
is already a huge bottleneck for you.

Fowler also mentioned how to handle datastores and so on,
quoting one rule from Amazon where they **do not allow
services to access other services' datastores**: though the
concept is very nice, elegant and cool I must admit that
sometimes you might want to make exceptions to this rule
as you need consistency over elegance; in general I would
try to stick to the rule but sometimes there are **problems
HTTP isn't prone to solve**, like transactionality.

{% pullquote %}
Additionally, sticking to the rule would create dependencies
between the 2 services rather than between a service and
a DB (which is usually much more robust and tested than our own services),
which means you really need to be good at microservices
before tangled in so many dependencies. What if an HTTP call
fails? What if service B is down?

The answer isn't simple but can be summarized in: {" rely 100%
on microservices when you're 100% confident on microservices "};
if you don't have much experience / great monitoring tools /
high availability you might want to slow down and make a
wise choice over an elegant one.
{% endpullquote %}

What is interesting is that it seems that, with the advent of
tools like Docker (and its own tooling like [Swarm](https://github.com/docker/swarm/), [Machine](https://github.com/docker/machine) and [Compose](https://github.com/docker/compose))
a lot of engineers started to see greater potential in
microservices simply because they are now much easier
to employ. Heroku tried to open up the way, years ago,
with its [12-factor apps](http://12factor.net/), and today
small, simple, interconnected architectures seem as real as ever.


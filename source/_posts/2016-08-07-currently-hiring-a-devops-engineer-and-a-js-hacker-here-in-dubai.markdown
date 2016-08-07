---
layout: post
title: "Currently hiring: a DevOps engineer and a JS hacker here in Dubai"
date: 2016-08-07 11:29
comments: true
categories: [hiring, team]
author: Alessandro Nadalin
---

Hey folks, here at [Namshi](https://www.namshi.com) we're currently looking
to beef our team up -- without further ado, let me quickly go through both
of them:

* **Sr. DevOps engineer**: we run our entire stack on AWS, and 99% of our
services run in Docker containers orchestrated via [Kubernetes](http://kubernetes.io/):
a big chunk of our infrastructure is provisioned through [SaltStack](https://saltstack.com/)
and we like to keep things simple, reliable and robust -- inspired by
[Netflix's dystopian architecture](https://www.infoq.com/news/2013/05/dystopia-as-a-service).
We believe in fault-tolerant, reactive architectures that don't care about a
server going down, as others will be able to take on the load, or automated checks
will make sure new HW comes online -- and that's how we setup our live infrastructure,
where a sizable chunk of our services runs through spot instances, with confidence :)
We are now looking for someone who can help us lifting the team a step further, playing
with tools like:

  * golang / python / bash
  * Docker
  * Kubernets / Swarm / Mesos
  * AWS
  * SaltStack / Ansible / Chef

In the past 18 months we've worked on a few interesting challenges such as:

  * implementing ChatOps
  * utilizing containers on our live infrastructure
  * integrating Terraform to automate tasks / provisioning
  * reduced our AWS bill, while growing as a company, by writing more efficient
  applications and introducing spot instances in our fleet

This gives you an overview of the kind of stuff we would love you to work on:
we think of DevOps engineers as a mix between software developers
and system administrators, so we'd like you to know a bit of both :)

* **(Sr.) JS engineer**: it is no news that we've been exploring the JS ecosystem
for a few years now: from rolling out our first angular apps in 2013 to testing
React Native in the past months, we've been very busy trying to push our frontends
as far as possible.
We run a Service-Oriented architecture where JS plays a huge part: most of our
services are either SPAs or small NodeJS-backed APIs, and JavaScript is king at
Namshi.
We would like to work with someone who has a very strong background in the language,
who's been battling on the frontend for a few years and is not afraid to dive into
Node, if required.
Some of the things our frontend team has been working over the past few weeks:

  * React Native
  * Redux
  * MobX
  * isomorphic JavaScript
  * AMP

Most of our frontend apps are built with Angular, although we're heavily experimenting
with React, even for our internal tools. With a fleet of 50+ microservices, we're
generally very busy trying to innovate as much as possible.

We're open to very promising, junior candidates even though we're currently hoping
to find someone with a few years of experience on his / her back -- if you feel you
do not have the experience but have the right attitude, then let's definitely have
a chat.

What are you waiting for? Drop a line at `work-in-tech@namshi.com` and let's
start this journey together!

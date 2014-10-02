---
layout: post
title: "What went down at SymfonyLive London?"
date: 2014-09-28 01:26
comments: true
author: Mohamad Hallal
categories: [symfonylive, conference, presentations, talks]
---

{% img center /images/symfonylive.png 640 640 'SymfonyLive' 'SymfonyLive' %}
SymfonyLive London 2014 concluded with top-notch presentations from Symfony and industry gurus. It was an exceptional
opportunity being able to attend this conference and meet those people.

Here is my summary about some of the talks I had the chance to attend:

<!-- more -->

##Keynote: Understanding Craftsmanship by [Marcello Duarte](https://twitter.com/_md)

Marcello delivered an outstanding keynote presentation on achieving craftsmanship in software.
He provided a walk through the [Software Craftsmanship Manifesto](http://manifesto.softwarecraftsmanship.org/)
and how the Agile methodology should have been evolved to consider software craftsmanship as one of its practices.

The talk focused on:

* Delivering a well crafted software by following good practices such as clean code
* Being steadfast by considering yourself a craftsman rather than an executionar
* Focusing on the community by having mentors and supporting other programmers
* Enhancing productivity by dealing with customers as partners who understand your passion and approach
in delivering code rather than focusing on completing tasks

##The dependency Trap by [Jakub Zalas](https://twitter.com/jakub_zalas)

Jakub explained how bad it can be having a number of services depend on one external service and at a certain point,
when that external service needs to be replaced, you fall into what is called 'The Dependency Trap' due
to high coupling.

The approach to follow in order to avoid such situations is to work on making internal services
loosely coupled from external ones by introducing interfaces and 'Dependency Inversion'. The guidelines
of the Dependency Inversion principal are:

* High level modules (your services) should not depend on low level ones (libraries) instead, they should
depend on abstractions. In other words, both your services and the library should depend on interfaces that clearly
describe what they are expected to accomplish.
* Abstractions should not depend on details. This means that your interface should not be a detail in a possible
more generic interface.

Here at Namshi, we are introducing these practices, and as a starting point, we are loosely coupling our services from doctrine. It is
amazing to notice that we are on the right track and addressing industry wide issues!

##The Naked Bundle by [Matthias Noback](https://twitter.com/matthiasnoback)

This was one of the most contreversial talks at the confrence! Matthias's goal through this talk was to motivate the symfony
community to start considering the fact that one day, they will be faced with a situation where they have to port their
code to a different framework. Motivated by that fact, he started exploring ways that allow a Symfony2 based codebase to be easily portable
to other frameworks and thus, depend less on the Symfony2 framework itself. Now the good part is that most of Symfony is based on components, however,
Bundles are not. He moves on by trying to introduce workarounds for controllers, entities and templates to reside outside the bundle and create the
'Naked Bundle'.

##Sylius e-commerce for symfony2 developers by Pawel Jedrzejewski

A walk through Sylius, a powerful Symfony2 based E-commerce platform. Pawel explained how to start with Sylius, the available
bundles and components it introduces and how it can be used in non-Symfony codebase.

##Decoupling with design patterns and symfony DIC by [Konstantin Kudryashov (everzet)](https://twitter.com/everzet)

One of the best presentations that took place at SymfonyLive, Everzet just rocked it! He introduced the techniques he
followed in building Behat v.3. His main goal was to try and deliver the most extensible test framework while
maintaining backword compatibility. He used these goals and techniques as examples on how Symfony2 based projects
should be built.

The main guidelines to achieve decoupling, extensibilty and backword compatibility are to:

* Avoid premature abstractions.
* Close your code for any extensions (by using private attributes and final classes, for example).
* When a use case that requires opening up a class or an attribute rises, identify a proper extension point and put lots of thought in it.
* Whenever opening up a class or an attribute, keep in mind backward compatibility.
* Utilize the Gang of Four design patterns wherever needed!

In case of Behat, achieving the framework design goals was done by leveraging compiler passes as extension points and
using Delegation Loop, Decorator, Observer, Compsite, Chain of Responsibility and Adapter patterns. Each pattern was
used wherever necessary, for example, the Adapther pattern achieved the goal of backword compatibility by executing the
required transformation on legacy input and 'adapt' it to the new one.

Basically, the solution to a desing problem must reside in one of the design patterns in the Gang of Four!

##Keynote: Doing Everything With Nothing, Ephemeralization In The Cloud by [David Zuelke](https://twitter.com/dzuelke)

A very inspiring talk by David, he walks through the history of the evolution of daily life tools and how they keep doing
more with less (Ephemeralization).
From that point onwords, he explains how this observation relates to the computing world. We see that the cloud is
evolving daily by intorducing enhancements that makes developers' life easier and making them focus on what is important to them.
Infrastructure-as-a-service (IaaS) and Platform-as-a-Service (PaaS) are examples of this evolution. He moves on by introducing heroku,
a PaaS, and how it contributes to this fact by providing out of the box system solutions to developers.



All in all, the talks were inspiring and addressed current issues in web development. Can't wait for SymfonyLive London 2015!
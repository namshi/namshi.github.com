---
author: Geshan Manandhar
layout: post
title: "5 DevOps practices you should consider starting today"
date: 2014-09-01 09:33
comments: true
categories:
- DevOps
- php

---

DevOps simply stresses on communication, collaboration and integration between software developers and information technology (IT) operations professionals a.k.a sys admins. it's goal is to support automation and maximize predictability, efficiency, security and maintainability of operational processes, meaning you can deploy 5 times on production on the same day with confidence that all systems will be fully operational. This post is moreover a software enginner's point of view on the DevOps side.
<!-- more -->

It goes without mentioning, to build a robust architecture you will need to follow the industry standards for example, use a distributed version control system - [git](http://git-scm.com/) is a great one, use automated deployment tools - you should check [Capistrano](http://capistranorb.com/), write automated tests using framework like [PHPUnit](http://phpunit.de/), have some form of continuous integration - you can run automated tests with [Travis CI](https://travis-ci.com/) on different versions of a language and have some internal documentation in place which includes basic to advanced things about the codebase and the systems used. Following similar rules, at [Namshi](http://www.namshi.com) we use some [DevOps](http://en.wikipedia.org/wiki/DevOps) practices which are very helpful to us, here are some main ones:


## Virtual Development Environment -Vagrant

[Vagrant](http://www.vagrantup.com/) is a virtual development environment that works as controller to a virtual machine on your main machine. The virtual machine has all needed software stack installed, each software is installed as a config. It creates a reproducible and portable development machine which is shared among all the developers, that in turn eliminates "Works on my machine" syndrome and also help to get started with working on the codebase in a matter of hours compared to a day.

With use of vagrant, it is very easy to install new software and update them. You just add some scripts to how the vagrant box is configured and push the changes. All the software engineers will pull it, reload the vagrant machine and done. All of them have the new software and the correct version of it. As the configs, services and even file paths are shared everyone is on the same page when discussing about the system. If you are already interested in vagrant don't hesitate to start reading it's [docs](http://docs.vagrantup.com/v2/getting-started/index.html).


## Atomic Deployments
Some months back we used to follow a revised version of [gitflow](http://nvie.com/posts/a-successful-git-branching-model/) and grouped multiple tickets in same deployment, basically some completed tickets (generally 4-8) where merged to the `develop` branch and then merged to the  `release` branch. A tag was created from the `release` branch say `1.2.3` and it was deployed to `staging` then `production` after tests were ok. If all goes well the tag is merged to `master`. Issues with this was, if we had a problem after the deployment pinpointing which ticket introduced this issue was difficult. 

Lets fast forward to now, currently we do **atomic deployments**, it means only one ticket is deployed at a time. Each ticket has it's sole deployment. First the ticket is deployed to `staging` tested and if everything is fine then it's deployed to production. 

So, you completed your ticket all the automated tests have passed and your code has been reviewed, then 

1. You deploy it on `staging` environment after making sure that your branch is rebased with latest `master`. 
1. If all tests are fine a tag is created from your ticket's branch say `1.2.3`.
1. The tag `1.2.3` is deployed to production. 
1. If all goes well on production, then the tag `1.2.3` is merged to master. 

The above steps are carried out for each completed ticket. In case of **atomic deployment** if anything goes wrong, we know what is the cause of it as a ticket is generally a small unit of work. In both cases atomic or grouped deployment, `master` is always the stable branch deployable to production anytime. 


## Multiple staging environments

As we adopted **atomic deployments**, we needed to wait for the staging environment to be free to test our completed ticket(s). Sometimes the wait was more that a day if we had multiple tickets that had higher priority. There by, we devised a new way to make multiple staging deployments possible, how? The basic idea is:

1. Deploy with a `standalone` deployment flag for the ticket branch let's say `cancel-email`. (Standalone deployment will mean it will be deployed to a folder on the `staging` server same as the branch name which is `cancel-email` in this case.) 
1. To access codebase for this deployed branch (`cancel-email`), the software engineer who deployed will create a cookie lets say `staging-route` on the browser with value of the branch name `cancel-email` and send request from the browser which has this **magic** cookie.
1. Nginx will read the `staging-route` cookie if it's not set, it will route the request to the main staging. If the `staging-route` cookie is set Nginx will route the request to the branch name folder which will be something like `/path/to/codebase/cancel-email` and not `/path/to/codebase/main`.

Simple yet effective solution to having a single staging problem. We are still improving it to be used in all our use cases, but the basic idea makes it possible to test multiple tickets independently and without waiting for the **main** `staging` environment to be free. We used `cookie` to achieve this instead of URL parameters or subdomains because we can do cookie forwarding that works for all our apps which use Nginx or NodeJs.

## Active logging and monitoring

We are very keen on logging the events that take place in our system for example, if an email is sent notifying a customer about order being shipped, it is logged. As the number of logs is huge, to help ourselves from drowning in the text logs we use some log management and monitoring tools that help both the development team and the sys admins team. We currently use [NewRelic](http://newrelic.com/), [Graylog2](http://graylog2.org/) and [PagerDuty](https://www.pagerduty.com/) with a mix of some internal notification systems. These useful tools give us a real time view of what is happening on the systems as well as the health report of the systems. 

For software engineers, it is always good to know that the code you wrote is behaving properly as you expected, this is where the Reports from NewRelic and the deliberate logs in Graylog come in very handy to know how the code is working.

##Zero Downtime Deployment

I remember the days around two years back, whenever we deployed to production it would have ~1 minute of downtime (maintenance page) for the files to be pulled and the database migrations to run and other tasks. Now, we see almost no downtime during deployments. It has been possible because of [Capistrano](http://capistranorb.com/). When deploying it performs all the tasks and if all tasks are fine then just switches the symlink to point to the new code resulting in almost zero downtime deployment. For NodeJs we use [clusterjs](http://tech.namshi.io/blog/2014/06/27/clusterjs-clusterify-your-nodejs-applications-and-achieve-zero-downtime-deployments/), which reloads a cluster in place of restarting it and eliminating any downtime without dropping any connection, thus we don't need the maintenance page any more.

Zero Downtime Deployment in case of working system that needs feature upgrade is a shared responsibility between the development team and the sys admins team. In case it's related only to systems like upgrading MYSQL from 5.5 to 5.6, there will be nothing the software engineer can do while the upgrade happens, but before the upgrade the impact analysis should be done where both teams will need to collaborate. There should also be a contingency plan for rollback for each upgrade or new system deployment. Even when working for tickets that need big changes, we as software engineers implementing it should always think about backwards compatibility which can make sure least possible downtime.


If DevOps is about more collaboration and communication between the development team and the sys admins team to achieve higher predictability, maintainability, security and efficiency we are definitely striding to the right direction. We are looking for a [devOps engineer](http://tech.namshi.io/join-us/), if you think we are applying best practices in devOps and you want to join a vibrant team at Namshi get in touch.

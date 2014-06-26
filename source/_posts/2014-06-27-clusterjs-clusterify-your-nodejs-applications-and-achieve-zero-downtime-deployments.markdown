---
layout: post
title: "ClusterJS, clusterify your NodeJS applications and achieve zero-downtime deployments"
date: 2014-06-27 00:45
comments: true
categories: [NodeJS, cluster, JavaScript, namshi, open source, OSS, ClusterJS]
author: Alessandro Nadalin
---

NodeJS has a very nice built-in support for clusters
through its native [cluster module](http://nodejs.org/api/cluster.html):
by using it and realizing we wanted to achieve zero-downtime
deployments (ZDD) on our node apps we decided to kick in.

<!-- more -->

This afternoon [Lucio](/team#Luciano Colosio) came up to me
and asked me a simple question:

> Let's do some NodeJS today?

and my answer was clear and simple: we need a robust solution
for ZDD, let's do this.

Before jumping with our usual `git init` and `npm init`
we searched a bit and found a few alternatives, which
weren't exactly fitting our scenarios:

* [learnboost/cluster](http://learnboost.github.io/cluster/), which is 
quite old, discontinued and hasn't been updated in a couple of years
* [isaacs/cluster-master](https://github.com/isaacs/cluster-master), which
seems to have a whole lot of great things but we wanted to have
the startup script as a global command rather than having to write
a script on our own (but we might wrap it, as it
has a lot of cool features, like resizing your cluster)

At the end we decided to go for a simple solution after reading
[this post from Jax](http://jaxbot.me/articles/zero_downtime_nodejs_reloads_with_clusters_7_5_2013)
that explains how to achieve ZDD on NodeJS.

In a few, very productive hours, Lucio was able to write
[ClusterJS](https://www.npmjs.org/package/clusterjs) and
we decided to release it to the public, as it might
be interesting out there (and because we really like
to receive feedbacks!).

Suppose you have a typical [ExpressJS](http://expressjs.com/)
app that runs through an `app.js` script, in order to run it through
ClusterJS you simply have to:

```
npm install -g clusterjs

clusterjs path/to/app.js
```

At this point you should be able to see something like this:

```
~/projects/clusterjs (master ✔) ᐅ clusterjs test/testApp/app.js

Booting a cluster of ./test/testApp/app.js
We are gonna be launching 4 workers
The cluster will reload on: SIGUSR2
--- test app loaded as pid: 21543
--- test app loaded as pid: 21544
--- test app loaded as pid: 21546
--- test app loaded as pid: 21547
Worker 1 running!
Worker 2 running!
Worker 3 running!
Worker 4 running!
```

Cool! You have just put your app in a cluster with four workers!

By default, ClusterJS will boot as many workers as CPUs
in your system; if you wanna change it, simply:

```
~/projects/clusterjs (master ✔) ᐅ clusterjs test/testApp/app.js --workers 10

Booting a cluster of ./test/testApp/app.js
We are gonna be launching 10 workers
The cluster will reload on: SIGUSR2
--- test app loaded as pid: 21636
--- test app loaded as pid: 21635
Worker 2 running!
Worker 1 running!
--- test app loaded as pid: 21650
--- test app loaded as pid: 21640
Worker 9 running!
--- test app loaded as pid: 21639
--- test app loaded as pid: 21641
Worker 4 running!
--- test app loaded as pid: 21644
Worker 5 running!
Worker 3 running!
Worker 6 running!
--- test app loaded as pid: 21648
--- test app loaded as pid: 21646
Worker 8 running!
--- test app loaded as pid: 21652
Worker 7 running!
Worker 10 running!
```

How to achieve ZDD? Simply issue a SIGUSR2 command
to your master process, after updating the files
in your app:

```
~/projects/clusterjs (master ✔) ᐅ clusterjs test/testApp/app.js --workers 2
Booting a cluster of ./test/testApp/app.js
We are gonna be launching 2 workers
The cluster will reload on: SIGUSR2
--- test app loaded as pid: 22007
--- test app loaded as pid: 22008
Worker 1 running!
Worker 2 running!


SIGUSR2 received, reloading the app
*** reloading workers!
restarting worker: 1
Shutdown complete for worker 1
Worker 1 died :(
--- test app loaded as pid: 22018
--- test app loaded as pid: 22021
Worker 3 running!
Worker 4 running!
Replacement worker online.
*** reloading workers!
restarting worker: 2
Shutdown complete for worker 2
Worker 2 died :(
--- test app loaded as pid: 22055
--- test app loaded as pid: 22056
Worker 5 running!
Worker 6 running!
Replacement worker online.
```

If you want to change the signal used to reload the cluster,
you can do it with a simple option:

```
clusterjs test/testApp/app.js --workers 2 --reloadon SIGUSR1
```

That's (basically) it! Kudos to Lucio for his work and...

...if you wanna play with ClusterJS or suggest any improvement,
feel free to have a look around, as
[ClusterJS is on GitHub](https://github.com/namshi/clusterjs).


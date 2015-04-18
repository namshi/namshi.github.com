---
layout: post
title: "godo, our first open source library written in golang"
date: 2015-04-18 19:34
comments: true
categories: [golang, open source, oss]
author: Alessandro Nadalin
---

Time for some more OS goodness in these days at
[Namshi](https://www.namshi.com): this time we've
been experimenting with [Go](http://golang.org/)
and decided to release a small project we've built
in the last couple of days.

<!-- more -->

The library, called [godo](https://github.com/namshi/godo)
(which literally means *go and do*) is trying to
solve the problem of always doing repetitive tasks
on remote servers (remote execution) without having
to use more advanced tools like Fabric / Salt / etc.

Tired of "*let me check what's up Varnish on our frontend servers*"? Enter godo.

With godo, you define a configuration file, put it in your
home (on your local machine) and you can start invoking
remote commands.

This is how your configuration file would look like:

``` yaml ~/godo.yml
servers:
  web1:
    address: "xxx.xxx.xx.xxx:22"
    user:    "me"
  web2:
    address: "xxx.xxx.xx.xxx:22"
    user:    "me"
  db:
    address: "xxx.xxx.xx.xxx:22"
    tunnel:  "tunnel.yourcompany.com:22"
    user:    "me"
groups:
  web: [web1, web2]
commands:
  uptime:
    target:       all
    exec:         "uptime"  
    description:  "Retrieves uptime info for all machines"  
  slow-queries:
    target: db
    exec: "mysql-log -n 10"
  nginx-logs:
    target: web
    exec: "sudo tail -10f /var/log/nginx/access.log"
hostfile: "/home/YOU/.ssh/known_hosts"
timeout: 2
```

and here's a little GIF that is pretty self-explatory:

{% img https://raw.githubusercontent.com/namshi/godo/master/images/gif-example.gif %}

For more infos, please refer to the [usage](https://github.com/namshi/godo#usage) section of the [readme](https://github.com/namshi/godo).

## A bit of background...

We've been starting to play around with Go since we believe it
is a solid language for DevOps-ish things and is overall interesting
and simple enough when you have to deal with stuff that needs
to be running on different platforms -- in fact, before godo
we started hacking on go to write some small programs that run
at our warehouse in Dubai, and run on windows :)

Of course, Go is very different from other players in our stack
(ie. JavaScript and the good old PHP) and serves very different
purposes, but seems like we will be experimenting much more in the
next months.

Though, don't worry, we <3 JavaScript and aren't planning to
leave it anytime soon ;-)
















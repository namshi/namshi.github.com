---
layout: post
title: "Sending files in unix: the quick and dirty way"
date: 2015-04-19 09:39
comments: true
categories: [unix, shell, network, command, vintage]
author: Luciano Colosio
---

You're hacking around with a friend, and you need to send him a file. How would you do that?

Just use a USB stick (do people still carry those things around?).

With the "power of the Internet" you have a bunch of possible ways to do it with a simple journey around the planet:

- send an email
- put it on dropbox/gdrive/box/etc
- skype/facebookMessanger/*chat*/*IM*
- ...
<!-- more -->
After all, who doesn't have an internet connection these days?

I'm an old guy and back when I was in college, having an internet connection wasn't that obvious, but you could almost always count on a 
[cat 5](http://en.wikipedia.org/wiki/Category_5_cable) [twisted pair](http://en.wikipedia.org/wiki/Twisted_pair) with an [rj45](http://en.wikipedia.org/wiki/RJ45) plug. Why? Simple - stumbling on a sudden [LAN party](http://en.wikipedia.org/wiki/LAN_party), or just craving a quick [frag](http://slanggeek.com/Slang/Details/frag) with a pal in your fav map on [Quake3](http://en.wikipedia.org/wiki/Quake_III_Arena) or [Unreal](http://en.wikipedia.org/wiki/Unreal_Tournament) was way more common than finding a USB stick or a wifi connection in an age they didn't yet exist.

So now you have a nice TPC/IP connection you can leverage on (but no internet). Great, what to do?

NFS/SMB share? Perhaps not: there were always concerns about security. [Iptables](http://en.wikipedia.org/wiki/Iptables) were tighter than my belt, after living almost an year in Dubai and gaining 10KGs; plus configuring one of those shares from scratch used to take more time than just spelling out the file bit by bit and have your friend manually type it.

Second option: we have [SSH](http://en.wikipedia.org/wiki/Secure_Shell)! It's secure, and comes with [scp](http://en.wikipedia.org/wiki/SCP)!<br>
Great! <br>
NO! <br>
No way I'd give remote shell access to someone to my laptop. Right?

FTP, WEBDAV, and a plethora of other options could have probably done it, sure. With configuration or giving account on your machine... See what I mean?

Today probably some young entrepreneur would come up with a whole startup to solve this problem, however, our simple solution used to be: [netcat](http://en.wikipedia.org/wiki/Netcat), the TCP/IP swiss army knife.

NC (short for netcat) can open tcp sockets for both reading (listen) and writing:

```bash
#read/listen
nc -l <port>

#write
nc <host> <port>
```

So, how do you do it? Simple:

Just set up a listener on the destination machine and divert it's output into a file:

```bash
nc -l 1234 > destination.jpeg
```

and simply cat it to netcat on your sending machine:

```bash

cat source.jpeg | nc <destination host ip> 1234
```

Add sugar and gpg for any extra privacy, because if you do it on you campus' internal network, you don't want sniffers to grab your stuff ;)

The same trick works with a directory. It's unix, just add pipes and some other commands:

receiver:
```bash
nc -l 1234 | tar x
```

sender:
```bash
tar -c <your dir name> | nc <host> 1234
```
And that's all :)

PS: Thanks to [David](/team#David Funaro) and [Filo](/team#Filippo De Santis) for reminding me about those days when learning and using old tools was simpler than making a new company ;P
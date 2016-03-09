---
layout: post
title: "Docker security improvement - Username Space"
date: 2016-03-09 10:00
comments: true
categories: [ docker ]
author: David Funaro
---

{% img center /images/posts/docker-share-volume.png %}

With the 1.10 release, Docker added a huge list of new features. Whith this post we are going do analyze one of options: username spaces.

Prior to version 1.10, running an alpine container mounting an external volume `/var/log/` was done as follows:

```
➜  ~  docker run -it --rm -v /var/log:/var/log --name=demo alpine ls -latr /var/log/
total 35236
drwxr-xr-x    2 root     root          4096 Oct  5 15:57 apt
drwxr-xr-x    2 root     root          4096 Oct 20 16:18 dist-upgrade
-rw-r-----    1 root     adm             31 Oct 21 17:28 dmesg
-rw-r--r--    1 root     root         72557 Oct 21 17:29 bootstrap.log
drwxr-xr-x    2 root     root          4096 Nov  5 08:19 fsck
drwxr-xr-x    3 root     root          4096 Nov  5 08:49 installer
-rw-r-----    1 root     adm           6378 Dec 15 12:18 apport.log.1
drwxr-x---    2 root     adm           4096 Dec 17 06:04 unattended-upgrades
-rw-r-----    1 104      adm        1351288 Dec 19 05:56 syslog.2.gz
-rw-r-----    1 104      adm         402127 Dec 25 06:17 auth.log.1
-rw-r-----    1 104      adm        8667895 Dec 25 06:17 kern.log.1
-rw-r-----    1 104      adm         932806 Dec 25 06:21 syslog.1
```

Notice that the docker deamon is running with no additional options.

```
➜  ~  ps -ef | grep -i daemon
root      21636      1  2 15:04 ?        00:00:00 /usr/bin/docker daemon -H fd://
```

The ownership of the files in the container is exactly same as the host. The user inside the container as full rights to the files. With this access, the user can even delete the files which will delete the files in the host as well. This is the problem!

Running the same code with Docker 1.10, adding the new option `--userns-remap=default` to the deamon, this results to:

```
➜  ~  ps -ef | grep -i daemon
root      21636      1  2 15:04 ?        00:00:00 /usr/bin/docker daemon -H fd:// --userns-remap=default
```

As seen below, the deamon is running with an additional option `--userns-remap=default`.

```
➜  ~  docker run -it --rm -v /var/log:/var/log --name=demo alpine ls -latr /var/log/
total 35260
drwxr-xr-x    2 nobody   nobody        4096 Oct  5 15:57 apt
drwxr-xr-x    2 nobody   nobody        4096 Oct 20 16:18 dist-upgrade
-rw-r-----    1 nobody   nobody          31 Oct 21 17:28 dmesg
-rw-r--r--    1 nobody   nobody       72557 Oct 21 17:29 bootstrap.log
drwxr-xr-x    2 nobody   nobody        4096 Nov  5 08:19 fsck
drwxr-xr-x    3 nobody   nobody        4096 Nov  5 08:49 installer
-rw-r-----    1 nobody   nobody        6378 Dec 15 12:18 apport.log.1
drwxr-x---    2 nobody   nobody        4096 Dec 17 06:04 unattended-upgrades
-rw-r-----    1 nobody   nobody     1351288 Dec 19 05:56 syslog.2.gz
-rw-r-----    1 nobody   nobody      402127 Dec 25 06:17 auth.log.1
-rw-r-----    1 nobody   nobody     8667895 Dec 25 06:17 kern.log.1
-rw-r-----    1 nobody   nobody      932806 Dec 25 06:21 syslog.1
```

The result is, owner is `nobody` now. Even the  root user of the container cannot change the files. This is a great security upgrade that everybody was waiting for.

>
> This feature saved us from working for a custom solution to tackle this problem.
>

* image source: [Jeff Nickoloff - on-docker Blog](https://medium.com/on-docker/what-s-montague-docker-user-problems-and-patterns-79750c504aa1#.pmer584z9)


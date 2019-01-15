---
layout: post
title: "From cloud to cloud: how Namshi migrated a 6yo AWS infrastructure to GCP"
date: 2019-01-15 11:23
comments: true
categories: [gcp, aws, gke, kubernetes, sre]
author: Abdelrahman Shiddo
---

Our new year started with system-fireworks!

{% img center /images/fireworks.png %}

On January 1st, Namshi moved the majority of its infrastructure to Google Cloud Platform in order to take advantage of GKE, the incredible managed-Kubernetes service GCP offers. When you visit *namshi.com*, you will be served from the new infrastructure we migrated to, which includes our web applications as well as database servers.

This concludes an activity we initially thought of **a year and a half ago** and started working towards in Q2 2018.

In this post, we’d like to describe both how we migrated 6 years of infrastructure from AWS to GCP as well as the toughest challenges we faced along the way.

<!-- more -->

## Why the move?

At Namshi, we heavily rely on Kubernetes to run our web services workloads as we aim for a microservices architecture. At first, we used [Salt](https://www.saltstack.com/)[Stack](https://www.saltstack.com/) to provision our Kubernetes clusters on EC2 instances but later moved to [Kops](https://github.com/kubernetes/kops) as it was easier to manage and create clusters, however it still felt a bit tacky.

We were looking for a cloud provider that integrated seamlessly with Kubernetes and looking at GKE, it was Kubernetes native which gave it a lot of advantages compared to others such as:

- managing networking and scaling
- everything is one place, from the dashboards to draining a node to 
- pod logs and cluster metrics are sent automatically to Stackdriver which gives incredible insight
- cluster upgrades are done within a click of a button 

## Planning

We kicked off our journey by first meeting with the Google engineers, led by [Ziad](https://www.linkedin.com/in/ziad-jammal-656a5654/), to understand what GKE had to offer and how to fully take advantage of it. Other than our Kubernetes workload, we have databases running in RDS and Elasticache, so it was vital to know whether or not we ’d be migrating our databases. We also ran a good chunk of our workloads on [spot instances](https://tech.namshi.io/blog/2017/07/09/running-spot-instances-in-production/), so that’s something we would have like to keep on GCP.

Following the meeting, we concluded that using spot instances (aka preemptible nodes in GCP) to run a majority of our workload wouldn't be as straightforward due to the termination of instances after 24 hours and no guarantees in terms of termination notifications. We’d also have to find a way to replicate from RDS to CloudSQL and later promote it to master, as going the good old fashioned way of `mysqldump` would have been pretty risky. We compared MemoryStore to Elasticache and found that MemoryStore wasn't mature enough so we decided to stick to Elasticache in AWS. 

Putting our staging environment on GCP was the first stepping stone to the big move. It was essential to get familiar with Stackdriver, managing the cluster from a simple UI, performance testing our applications with CloudSQL, however Elasticache and SQS were still running on AWS which may cause latency issues.  It also gave our devs a chance to play around with the powerful logging and monitoring tool Stackdriver has to offer, which they choose over Prometheus for application metrics. 

## RDS Issues

The most vital part of the whole migration was achieving a reliable/consistent replication process from RDS to CloudSQL.

Our first gut instinct was to use CloudSQL’s [migrate data](https://www.google.com/search?client=opera&q=cloud+sql+migrate+data&sourceid=opera&ie=UTF-8&oe=UTF-8) feature that lets you replicate from an external source such as AWS or on-premise, but it required the source to have GTID enabled which AWS didn't have at the time. All our time went into finding a seamless method to replicate the data using tools like [Attunity Replicate](https://www.attunity.com/products/replicate/) and [Percona XtraBackup](https://www.percona.com/doc/percona-xtrabackup/2.4/index.html) that weren't very reliable because of how long it took (we also observed inconsistent data from import to import).

Luckily, on the 12th of October AWS announced the support of GTID on MySQL 5.7.23. This required downtime of around 10 minutes to upgrade our master instances and then replication would be as simple and reliable as ever from one MySQL instance to another across clouds using CloudSQL migrate data.  

## Miscellaneous Issues

Other than the RDS issues, we had a few issues here and there such as [kube2iam](https://github.com/jtblin/kube2iam), S3 and Elasticache latencies.

Kube2iam is an awesome tool that allows pods to authenticate using the EC2 nodes metadata instead of credentials. It made our lives a lot easier on AWS, but it wasn’t cloud agnostic at all. It required provisioning of new credentials and, in some cases, code changes to authenticate using credentials instead of metadata. 

While running tests on a replica of our production environment on GCP, the latency to SQS, Elasticache and S3 in different regions was a few seconds - we expected some latency but nothing this crazy!
We decided to migrate a few important S3 buckets using the cross-replication policy, provision new SQS queues and an Elasticache cluster in regions closer to GCP that saw the latency drop back down to a few hundred milliseconds and we can live with that.

## Big night

As the end of the year was approaching, we had to find the best time to perform the actual migration as it required 3 hours of downtime. We consulted our PM team about the scheduled downtime, turns out New Years 4 am was the best time to carry out such a risky and long migration.

Here’s a list of the actual migration steps we followed:

- inform all teams before the scheduled downtime
- take down all services
- lock writes to RDS
- run data consistency tests for both AWS and Clouds SQL to ensure no discrepancies
- cloud SQL instances were promoted to master
- test website and applications internally
- forward traffic from old cluster to new cluster in GCP in order to make sure that if a client doesn’t respect the DNS TTL we can still forward it to GCP
- switch our public DNS to point to GCP

Everything was followed as planned and the predicted timings we had for each task was spot on. However, nothing goes ever as planned as we had one problem due to insufficient memory on the new ElastiCahce instance which was fixed by upsizing the instance. Other than that, the whole migration seemed seamless and, for a second, we forgot how big of a task this was.

## Aftermath

At 7 am on January 1st, we brought all of our services all backup and watched our monitoring systems for any issues or anything unexpected. It's a big relief to say that we didn’t get any complaints from customers and other than the downtime, it seemed like nothing had changed.

The same can’t be said for a few of our internal tools, where we noticed a few problems, but were mostly due to them still pointing to the wrong MySQL endpoint or S3 bucket. The fixes were pretty straightforward and everything in our internal tools were back to normal.

After ensuring everything was running fine on GCP, it was time to scale down our old Kubernetes cluster running on AWS, as well as remove any RDS and Elasticache replicas.  

## Namshi still 💛 AWS

Something we’d like to clarify is that we still rely on AWS for a bunch of services, as we believe a world of multiple clouds allows us to pick the right tool for the job. There are some services we think are more suitable to be kept in AWS, and decided against migrating them — we might revisit these decisions later on but, for now, we’re happy with where we are.

AWS has served as a strategic partner for Namshi for over a lustrum, so we’d like to mention that we’re not running away from a bad provider, but rather that we found GCP more suitable for the kind of workloads and stack Namshi runs on.

## In the end…

We are very happy with this activity as it allows our infrastructure to run in an environment (GKE) that is more suitable for our stack. Additional benefits, like cost reductions and better integration with other parts of our stack (like data warehousing, which has been running on GCP since its inception), are secondary to the fact that we have eliminated in-house management of our Kubernetes clusters, a tedious activity we’d like GCP to take care of, in order to let us focus on our domain-specific challenges.

A special thank goes to [Andrey](https://tech.namshi.io/team/#Andrey%20Komarov), [Carles](https://tech.namshi.io/team/#Carles%20Iborra) and [Ayham](https://tech.namshi.io/team/#Ayham%20Alzoubi), as they shared the burden of this legendary task along the way, and sacrificed their NYE to let Namshi take a step forward!

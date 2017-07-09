---
layout: post
title: "Running Spot Instances in Production"
date: 2017-07-09 10:12
comments: true
categories: [aws, ec2, spot-instances, kubernetes, k8s, containers, sensu]
author: Oluwaseun Obajobi
---

Around this time last year, we decided to try running subset of our customer-facing web traffic on spot instances.<br/>
This decision was solely based to reduce our AWS instance bill. We've heard of people running workloads on spot instances but most of the workloads are usually long-running jobs where you don't mind if the instance gets terminated at any time. Running customer-facing apps is a completely different challenge where we can't afford any downtime of any sort.

### Background
We are fully running [kubernetes](https://kubernetes.io/) in production which makes it exciting for the challenge of how we can actually test chaos engineering in production with our microservices.<br/>
We chose [CoreOS Container Linux](https://coreos.com/os/docs/latest/booting-on-ecs.html) as our preferred operating system because of faster bootup time and it does only 2 things for us: docker service (for running containers) and flannel networking (for inter-pod networking).<br/>
We use both launch configuration and autoscaling service to manage our fleet of spot instance.<br/>

Some of the questions we asked oursleves on how to setup a robust infrastructure to support any kind of termination of the spot instances

1. How do we gracefully reschedule the pods to other nodes before the spot instance goes down?
2. How do we handle the surge in price for one availability zone?
3. How do we handle the surge in price for the whole region?

### How do we gracefully reschedule the pods to other nodes before the spot instance goes down?

Gracefully rescheduling pods initially do seem straightforward until we started noticing some issues with image pulling from our private registry and the docker hosts. This usually happens as a result of spike requests if more than ten (10) images of around 200MB size are being pulled at the same time. There is [kubectl drain](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#drain) which works pretty well but not for us because of the issue mentioned earlier.

Luckily, AWS introduced [spot instance termination notice](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html) which is a 2-min window to do cleanups before the spot-instance is terminated, we wrote a simple golang binary which watches the instance metadata for the termination notice and does the following within the 2-min grace:

* Detach the instance from the ELB (if applicable)
* Mark the instance as unschedulable
* Delete the pods with a sleep in-between of 10secs (This should take care of approximately 20pods, which is a very rare case for us)

This binary is managed by a systemd service

```
---
coreos:
  units:
    - name: spot-terminate-cleaner.service
      command: start
      content: |
        [Unit]
        Description=Graceful cleanup of spot instances before termination
        Requires=network-online.target
        After=network-online.target
        ConditionPathExists=/etc/spot

        [Service]
        ExecStart=/opt/bin/nm-tools spot-shutdown
        Restart=always
        RestartSec=10
```

### How do we handle the surge in price for one availability zone?

It is advisable to run the spot-instances in at least two availability zones to cope with surge in price in one of the availability zones. If there is a price surge above the bidding price in one zone and the spot instances are terminated, autoscaling group service automatically launches the same number of terminated instances in the zone(s) with bidding price higher than the current spot price. With this, we achieve something close zero-downtime during the re-scaling activity.<br/>

{% img center /images/spot-stage-1.png 500 price surge in one availability zone %}
<br/><br/>

This also poses another challenge when the spot price drops below the bidding price in the previously affected region. What happens is that two instances launched back in `eu-west-1b` while the same number of instances are terminated to balance the autoscaling desired capacity. In this activity, we are going to lose instances abruptly, but luckily AWS autocaling service has a feature called [lifecycle hooks](http://docs.aws.amazon.com/autoscaling/latest/userguide/lifecycle-hooks.html).<br/>


To avoid abrupty autoscaling termination, we added a lifecycle hook for `autoscaling:EC2_INSTANCE_TERMINATING` transition state with the notification target as SQS. This sends an event containing the instance to be terminated to the SQS. We now have a python script (can be converted to a lambda function) which:

* Consumes the SQS message
* Detach the instance from the ELB (if applicable)
* Mark the instance as unschedulable
* Delete the pods with a sleep in-between of 10secs (This should take care of approximately 20pods, which is a very rare case for us)
* Delete the SQS message once the task is completed

All the tasks above are completed within 2-min window to match the spot-instance termination notice period.


### How do we handle the surge in price for the whole region?

We use [Sensu](https://sensuapp.org/) as part of our monitoring stack and developed a simple sensu (ruby) check which compares the current spot price from AWS API against our bidding price used in the launch configuration. We do mark the check state as **warning** when the spot price is within the warning and critical threshold for all the zones in the region and the check is only marked as **critical** if the spot price is higher than our critical threshold in all the zones in the region. When the check state is critical, there is an auto-remediation script which switches the launch configuration of the autoscaling group for the spot instances from spot to on-demand (the script clones the current launch configuration, removes the spot price and replaces the launch config in the autoscaling group). With this, we don't end up with no running instances.

```
{
  "checks": {
    "lc_spot_price_check": {
      "command": "/etc/sensu/plugins-custom/check-lc-spot-price.rb -n namshi-spot -r :::aws.region:::",
      "subscribers": [ "aws" ],
      "interval": 60,
      "refresh": 14400,
      "handlers": [ "default", "ses", "remediator" ],
      "remediation": {
        "lc_spot_price_check_remediation": {
          "occurrences": [1, 2],
          "severities": [2]
        }
      }
    },
    "lc_spot_price_check_remediation": {
      "command": "sudo /usr/bin/salt-call spot_price.update_spot_asg namshi-spot ondemand=True",
      "subscribers": [],
      "handlers": [ "default", "ses" ],
      "interval": 10,
      "publish": false
    }
  }
}

```

<br/><br/>

So far, this has been working well for over a year without any major issues and we have been able to save between 35% and 45% on the instance cost since then.<br/>
Hope you can give it a try and feedbacks are appreciated.

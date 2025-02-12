+++
title = 'Phased Approach To Cisco NSO'
date = 2025-02-12T14:09:46+02:00
draft = false
+++

First time is always the hardest. It helps if someone has already gone down the road and is ready to share their lessons learned. 

Cisco NSO is a powerful network automation tool that can help network operations tremendously but is also known for a steep learning curve. This post aims to lay down an implementation pathway that enables fast wins and expands the scope gradually while avoiding overwhelming the engineering teams.

Finally, I believe this same implementation template can be used for any project that aims to seize control over IT resources using some sort of centralized automation capability. 

## WIN#1: System installation

Get the tool installed to a data center or another centralized location that has management network connections to the devices. Prepare to get the permissions, compute capacity, and firewall rules in place prior to starting the installation. 

## WIN#2: Take control over the devices

This requires connecting to the devices using strong authentication and onboarding the devices to NSO. After that, you can start managing all your devices from a single CLI which on its own is an amazing benefit. 

## WIN#3: Device templates

Use device templates to establish standards that all network device configurations should comply with. A typical example is to set all static global security and network settings under a device template. You can use simple variables in device templates to set, for example, an IP address to your Loopback interface.

## WIN#4: Audit

Audit the network using device templates and see where it needs fixing the most. 

## WIN#5: Remediate the issues

Apply the device template(s) to your devices to make sure they follow the standard you want to enforce. 

## WIN#6-N: Develop network services

It makes sense to start with the services that only affect a single device and then move on to more complex services that must be orchestrated on multiple devices.

The scope depends on the network designs you have. If you don't have a documented network design - stop and create one. When it comes to automation you have to have a clear understanding of what workflow/design/task you're automating first. 

You should develop only one service at a time meaning that if you have a layered solution start from the bottom and work up. In networking, it typically means: build L2 first, then basic L3 connectivity, underlay routing, move on to L2 VPN services, and then L3VPN. 

Close collaboration and arm's length distance should be a prerequisite for infra and dev teams. 

Don't try to automate everything. For instance, it's ok to begin with manually allocated variables and if needed automate them later on.

Automate only based on clear effort-benefit analysis. If you do a thing only once or twice per year is it worth automating.

```
time and cost to automate < time and cost to do * repetitions done
```

Proper planning, preparation and execution saves blood, sweat and tears. 
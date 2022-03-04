---
layout: post
title:  Uncertain Times
date: 2022-03-04
category: logs
---

This week has been one of the more uncertain times in this industry. We have seen an extremely fast moving threat landscape which has been closely linked to the developing situation in the Ukraine war. The number of activities has been so high that it is almost impossible not to be drawn into a rective mode. Which on its own is a job well done by the adversaries. 

Then again much of the basic laws of protection have remained the same and should help to make it through this. Follow the same guidance as before but re-visit your risk register to see which risks you have accepted in the past. Mitigate those risks, segment those environments and monitor them. Those weak spots are the likely vectors that the adversaries have been seen to utilize throughout the weeks of conflict. 

It seems that the modus operandi of the adversaries is to match the escalation in the kinetic world with a response in the cyber domain. For instance, earlier this week Finland decided to send weapons aid to Ukraine and was almost immediately targeted with DDoS attacks. One of the attacks that got media attention was a local bank called Nordea. Banks provide a convenient option for strong authentication accross a variety of govermental services making it a juicy target for DDoS. Crippling those authentication systems not much can be done securely in the Internet. 

Overseas in Japan, Toyota was struck by an attack which caused them to close down 14 factories in Japan. As mentioned above it is hard to prove who's behind each attack due to the velocity of the threat landscape. This said, the cyber attack took place just after Japan's announcement to join Western countries in response to the events in Ukraine.  

Both incidents are currently under investigation and as more information trickles in we may get more evidence that what we are seeing is in fact the fourth dimension of the warfare. It seems to escalates by the following steps:
- Pre-operation phase: Dis-information, political meddling and influencing social media 
- Staging phase: Web defacement, DDoS attacks and other service disrupting activities
- Operative phase Land, steal, destroy:
	- Wiper and ransomware attacks (often using Microsoft AD based vector)
	- Gaining control over the networking devices
	- Data leaks

The list above is not complete but the pattern is at least partially visible. Pre-operation phase seems to be the hot peace era activities, staging phase is a response to a policy change and operative phase is used to support kinetic / hybrid operations in the target country.

Over the past weekend it became clear that defenders are now making counter attacks of their own. One of the intresting stories was around Nvidia. It seems like after Nvidia's data got stolen, they launched an allegedly succesfull counter attack. The hacker left behind one of their VMs which gave Nvidea a backdoor to the attacker and allowed encrypting the stolen data and cutting of the remote connection. This story around Nvidia and Lapsu$ (ironically in Finnish: "a childish mistake" - which this OPSEC failure was from the attacker's behalf) is important for two reasons:
1. The focus is on Ukraine and Russia now. The cyber criminals will use this distraction to their benefit. 
2. There aren't not many reported cases that I know of enterprise counter-hacking. Perhaps due to legal matters that apply to enterprises. Nvidia being a publicly owned corporation must have had green-light to perform the counter-hack from their legal team. 

All this said about Nvidia is hanging on the matter that Nvidia actually commited the counter-hack. I tried to find a reputable source confirming this and couldn't. Which leads to the other possible scenarios that Nvidia's network has been breached by not one but many hacking groups.

The point of this week was that you must stop scrolling Twitter and news feeds and try to build a bigger situational image instead. It seems that this time triggers a response where people think more activity is good but it is not. Concentrate on reputable sources for threat intelligence. Avoid social media OSINT unless you have a person or a team how can check on it. Remember that social media awards from activity and clicks and the there are groups out there that use this situation to upgrade their follower base for monetary gain. Keep exercising the fundamentals of good security, put extra effort on hardening sprints and monitor your weak spots. Finally ppractise how to recover in case your systems become under attack.  

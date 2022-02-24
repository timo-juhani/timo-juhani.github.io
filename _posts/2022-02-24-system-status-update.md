---
layout: post
title:  System Status Update
date: 2022-02-24
category: logs
---

According to news sites the attack to Ukraine has begun early this morning. Operations typically has begun historically just before the break of dawn. Modern operations have a cyber dimension which is used to support or enable kinetic parts of the operation. 

It's obvious that cyber capabilities are used in Ukraine currenlty but what's not fully evident how much of them will spill over to other countries or perhaps in all countries on the globe. 

Looking at common Internet services' availability services like Facebook, Twitter, BBC, Signal, Telegram and Instagram are getting their IP addresses resolved globally on DNS systems (tool DNSChecker). It seems DNS is working normally.

Then moving on to Cisco's BGP Stream tool it seems there aren't AS outages in the vicinity of the area of operations. However, there is an ongoing outage in Venezuela. It affects AS 269782 and belongs to a local ISP called Network Speed.

So, BGP and DNS are working pretty normally. This is if you pay attention to global Internet reachability only.

From website reachability point of view there isn't anything abnormal at the moment. The total number of outages have remained roughly the same this morning compared to the rolling average (tool Solarwinds Pingdom). The vast majority of the outages are in Western European countries and US which is explained by the web hosting capacity placements globally.

Cisco's Thousand Eyes' Internet Outage Map has a few incidents that get highlighted. The biggest is "Near Des Moines, Iowa, US" affecting tens of interfaces for services provided by Microsoft and Twitter. Microsoft has a data center there which confirms the observation. Micorsoft's problem is local although GitHub which is part of Microsoft has had issues globally withing the last 24h. 

Twitter is a bit more curious case. It seems Twitter's network has been suffering from incidents throughout the last 24h. Sites in US, Germany, UK, Tokyo, Oman, Singapore and Japan have had application level issues covering 70+ servers worldwide. Also folks are complaining that the service isn't working (tool Downdetector). At the same time mainstream media filled with headlines along these lines "Twitter says it has taken accounts down which posted on Russian military". What's going on? I looked on it and couldn't make any other conclusion that they had capacity issues. This is a very like yet unverified reason which correlates to the situation in Ukraine.

---
layout: post
title:  "On Zero Trust Networking (Briefly)"
date: 2020-3-16
categories: ztn aci cisco cybersecurity
---

Zero Trust Networking (ZTN) is one of the main trends in the security minded networking industry today. While many have a tremendous appetite to implement ZTN capabilty only few are able to. Why?

To implement ZTN, you have to have an outstanding understanding on your enterprise applications. Understanding how the applications is and should be allowed to communicate with its own tiers and peer applications becomes a must.

In a nutshell, ZTN implements a logical firewall between every application tier and application. That's why ZTN must apply rules or contracts to allow only the bare minimum of the traffic. The traffic that is designed to be there and nothing else. Easier said than done when figuring out how tight rule set you should operate.

![Kuvahaun tulos haulle cisco aci contracts](https://kxiwq67737.i.lithium.com/t5/image/serverpage/image-id/36735i6DE0044188059CC3?v=1.0)

For example, Cisco ACI implements ZTN using contracts ([image source](https://community.cisco.com/t5/application-centric/aci-contract/td-p/3855931))

The solution for this is to run periodic application dependency mappings or have a tool that visualizes the current application flows in real time. This mitigates the main problem of running hundreds of applications that form up a massive, invisible web of dependencies. Not understanding what applications do and how tight control we should implement is the biggest obstacle for ZTN approach (based on my own experience).

Just remember even having 20/20 visibility to your application dependencies doesn't allow you to transform your production network to ZTN over night. However, it gives you your current situation which you can you consume to prioritize, plan and move your network to ZTN one application or application group at a time.

The battle against "permit any any" has begun.
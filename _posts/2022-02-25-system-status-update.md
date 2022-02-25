---
layout: post
title:  System Status Update
date: 2022-02-25
category: logs
---

D2 of the conflict in Ukraine. 

Analyzing the current status of the networks and systems globally. 

### ThousandEyes Internet Outage Map

Twitter continued having issues yesterday. The impact of the issue raised from 70+ servers to 110+ servers. The conclusion at the moment of writing is overloading of the platform that picked up the pace as we moved to time zones covering US daytime. 

Cisco's Meraki suffered two outages which is very odd as the cloud is known for it's almost perfect availability measured over an extendendly long period. This needs further analysis. Even more so as the outages happened on two continents with only ~2h in between. 

Microsoft has recovered from their DC outage in Des Moines, although GitHub still suffered from outages.

Hetzner Online - a German hosting and CoLo provider has also been suffering from outages during the past two days. 

### Pingdom

A spike of increased website outages 12h ago. Roughly 30 % higher than the rolling average of the reporting period. It's quite hard to point out the reason as the tool provides no enrichment to the data, just numbers. 

### DNSChecker

No exceptions observed for Signal, Facebook, Twitter or BBC. All names resolve everywhere. 

### BGPStream

Same situation. Some outages outside of the area of operations, but nothing that directly look suspicious. 

### Meraki Outage

With a reasonable time spent, I couldn't find a solid reason. I might need to contact them direclty to find the root cause. However, in the past it has typically been an issue of the cloud provider on which Meraki services are running. This doesn't mean anything, but gives a historical reference point.

### Conclusion

No conflict spillover observed at the moment of writing.

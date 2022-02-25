---
layout: post
title:  Cyclops Blink
date: 2022-02-25
category: logs
---

D2 of the conflict in Ukraine.

New malware found. They call it Cyclops Blink.

The malware seems to target home and small office network devices much like VPNFilter. It provides recon capabilities but also can establish C&C and information import/export operations.

It runs on Linux systems that is deployed on PowerPC architecture. So what does this mean in practice? PowerPC architecture can be found from automotive and defence industries (e.g. F-35 fighter subsystem). But since this is reported to be targeting home and SoHo networking gear, I really had to educate myself on the topic.

It seems PowerPC processors are indeed used in routers and route processors, for instance some older Cisco equipment like Cisco 7600 and Catalyst 6500 (not SoHo or home by any means) have them. Interestingly popular platforms like ASR 1000 series have them as well. Furthemore, it appears that PowerPC architecture is also widely adopted in enterprise and smaller switches. So it seems like a standard HW architecture choice for networking. Why?

Well, it seems to come down to processor requirements that network traffic has. PowerPC is based on RISC architecture whereas x86 is CISC. In RISC architecture each packet or task can be handled with the same CPU cycle giving a faster forwarding rate that CISC. This design requirement is obviously a primary reason why PowerPC is chosen as a target.

However, not all networking OS are based on Linux, which is key here. So the malware doesn't apply to all PowerPC based equipment. They must be running Linux based networking OS like OpenWrt. This explains why the target is mainly home and SoHo devices rather than enterprise devices as well.  

Under the hood, the malware runs as a process named "kworker[0:1]" which may look familiar to all Linux users. Using this process name it masks itself as a kernel process. It applies changes to iptables to establish C&C channels back to the attacker. The channel is encrypted using TLS tunnel and AES-256 encryption on the control commands themselves. Which makes it hard to detect or analyse. 

Talos has published a Threat Advisory: [link]("https://blog.talosintelligence.com/2022/02/threat-advisory-cyclops-blink.html")

They have also published signatures so that the wiper can be caught for instance with Snort.

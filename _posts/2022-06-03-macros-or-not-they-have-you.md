---
layout: post
title:  Macros or Not They Have You
date: 2022-06-03
category: logs
---

An odd looking Word document was captured on May 27th. The document uploaded from Belarus uses Word’s external link to load a remote HTML file which loads and executes a PowerShell one-liner to provide a remote shell to the attacker. Remote shell is a remote connection to the machine allowing executing anything on the machine. 
 
In the heart of the problem is that Word is executing code even though macros are disabled. It’s a zero-day vulnerability that triggers as soon as the user leaves Word’s protected view mode. The vulnerability has been named Follina.  
 
While living without MS Office may be almost impossible for most of us, it makes great security sense to stay informed how to use it safely. 
 
Microsoft has published guidance and Youtube is filled with PoCs. 
 
[link](https://msrc-blog.microsoft.com/2022/05/30/guidance-for-cve-2022-30190-microsoft-support-diagnostic-tool-vulnerability/)
 
[link](https://www.youtube.com/watch?v=dGCOhORNKRk)
 
A PoC has been conducted in which a smartphone’s touch screen has been controlled remotely. GhostTouch uses electromagnetic interference to fake touch points into a touchscreen without the need of touching it. Don’t leave your phone laying on a table when visiting public places. 
 
[link](https://www.usenix.org/conference/usenixsecurity22/presentation/wang-kai)
 
A German financial regulation authority BaFIN has issued a cyber security warning to Germany’s financial institutions. In general financial institutions have witnessed an increase in DDoS attacks ever since many countries started to support Ukraine’s war efforts. DDoS has a dualistic nature it has an interference and a feinting objective. Much like a boxer’s jab it tries to catch the attention of the defender so that some of the action happening elsewhere remain hidden. 
 
[link](https://www.bafin.de/SharedDocs/Veroeffentlichungen/DE/Meldung/2022/meldung_2022_05_31_erneuter_sicherheitshinweis_finanzwirtschaft.html?nn=17559840)
 
Current threat actors are using VPNFilter type attacks to create Tor-like networks using the exploited SOHO network devices. This network is used as a platform for further attacks. A cost-efficient global but anonym network with no permissions asked. 

---
layout: post
title:  In Retrospective
date: 2022-05-20
category: logs
---

Vmware warned their customers to patch their systems on Wed (CVE-2022-22972). This vulnerability enables a threat that allows authentication bypass on Vmware’s popular products such as vRealize Automation and Cloud Foundation. After a successful exploitation the attacker gains admin access with authentication. Their recommendation is patching with a sense of urgency. Luckily this doesn’t pose a threat on ESXi software.
 
[source](https://core.vmware.com/vmsa-2022-0014-questions-answers-faq#sec20356-sub2)
 
While staying up to date on the current situation, a retrospective view is a must to understand how we got here. PWC has published their “cyber year 2021” report. Key findings:
The number of published 0-day vulnerabilities has increased. This most likely sounds familiar due to previous threat updates. There are a couple of strategic developments here that should leave food for thought: 1) why is the number increasing and 2) what’s the bigger picture as we’re seeing an ever growing footprint of unpatched infra and more and more zero day threats. It’s clear that the reasons demand discussion but so does our response. How do we scale up our efforts while the number of threats grow.
Ransomware and more specifically Ransomware as a Service was (and is) the biggest threat for most companies. Attacks have a financial motivation. Ransomware attack works as a land and destroy (and loot) attack well also because the motivation can be said to be financial but can’t be proven.  That’s why criminal indictment is important – so that the motivation behind the technology is understood. Especially now.
Supply chain attacks are an important part of more developed attacks. The tactics rely on stolen but trustworthy digital certificates being used to sign software so that it appears legitimate during installation. Furthermore, the recent exploits related to open source software such as NPM are pointing us to a direction where the organization has an increased role in establishing trust on software (and hardware) they install in their environment. Develop, harden, isolate, monitor, scan and if nothing pops up make a decision whether to trust.
Quartermasters (yes, it now has a name it seems) or offensive software developers have started to sell their goods to companies and organizations. This a change in the culture – and perhaps demand as quartermasters have been selling their goods mainly to military customers. What’s the problem? Well, why does the companies and organizations feel need for offensive capabilities?
Digital surveillance has continued or increased during the last year. That’s why technology trends such as privacy enhancing computation are now being highlighted in the industry.
 
[source](https://www.pwc.com/gx/en/issues/cybersecurity/cyber-threat-intelligence/cyber-year-in-retrospect.html)
 
A joint security advisory about top 10 attack vectors by national authorities was published on Tue. On the list are the most discussed topics like: outdated software, misconfiguration or default configuration, weak password policies, unused services and open ports. While these basics should always be in check controls like:
enabling MFA,
securing remote access for unauthorized access (VPN or SASE),
protecting and understanding how to protect public cloud technologies  
having routines how detect and respond to phishing and endpoint activities should be on the high priority list waiting for design and implementation.

[source](https://www.cisa.gov/uscert/ncas/alerts/aa22-137a)
 
Greenland’s national health service has been down since May 9 (updated yesterday). Sometimes recovery is hard. Regular response practice is important. The mindset bias with this is not to practice because it costs and you don’t know whether you are attacked –  “are we wasting money?”. The return of investment in security can’t be measured (often) in money rather it’s measured in ounces of the life blood of the organization.
 
[source](https://sermitsiaq.ag/cyberangreb-giver-store-problemer-i-sundhedsvaesenet)

 

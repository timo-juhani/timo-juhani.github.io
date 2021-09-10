---
layout: post
title:  "Application Level Ecryption Is Not Enough - Alone"
date: 2019-12-8
categories: logs
---

It's pretty common that clients ask about the role of network encryption (VPNs, MACSec etc.) now that almost all web applications utilize TLS encryption. The short blanket answer is: absolutely needed. Although there are plenty of reasons supporting that opinion, I think the picture below should help you to make up your mind on this topic.

![Idea behind the MUSCULAR program, which gave direct access to Google and Yahoo private clouds, no warrants needed.](https://upload.wikimedia.org/wikipedia/commons/f/f2/NSA_Muscular_Google_Cloud.jpg)

MUSCULAR (DS-200B) surveillance program,  
source: [https://en.wikipedia.org/wiki/MUSCULAR\_(surveillance\_program)](https://en.wikipedia.org/wiki/MUSCULAR_(surveillance_program))

Few years ago, a surveillance program called Muscular exploited a design flaw in cloud applications in which only the traffic to web front-end was encrypted. The traffic within the data centers was in clear text. Taking into account the geographical dispersion of the data centers this made/makes the interception of the traffic more than possible for the interested party.

This is troubling since most enterprises have on-boarded cloud based workplace applications. The case is a reminder that you shouldn't expect that your provider has implemented comprehensive encryption by default. This was the caveat that Muscular exploited. Therefore, it's important to know how your provider has secured their production networks against attacks on confidentiality.

Similar outcome can be a result of operating hundreds of private applications that are of different generations. In this case, the problem lies in varying design requirements, implementation and the sheer volume. That's why it may be extremely hard or impossible to assure fully encrypted application layer even if you are operating your own applications. Typical examples include:

*   Web front ends deployed with vulnerable versions of SSL or
*   One tier of the three tier application doesn't provide any kind of encryption or
*   Like in the case above, there is no network level encryption between production sites.

Facts on the table, it is hard not to consider network level encryption technologies like MACSec and VPNs as an added layer of protection for the applications.
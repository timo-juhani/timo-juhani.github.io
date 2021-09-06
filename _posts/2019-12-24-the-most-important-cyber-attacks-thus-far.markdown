---
layout: post
title:  "The Most Important Cyber Attacks Thus Far"
date: 2019-12-24
categories: cybersecurity ransomware ukraine cyberwar mirai iot ccleaner
---

### Atlanta Ransomware Incident 2018

[https://en.wikipedia.org/wiki/2018\_Atlanta\_cyberattack](https://en.wikipedia.org/wiki/2018_Atlanta_cyberattack)

This incident showed the monetary impact a cyber attack can have on a community.  
City spent roughly 17 million USD to recover and remedy their services. That  
price tag keeps increasing according to press due to ongoing remediation  
efforts, which naturally does not compensate the data destroyed  
during the incident. Some newspapers have cited a GDP loss of 3 billion USD -  
quite a remarkable loss.

A modern city in one of the most advanced countries on the world put on its  
knees by two Iranian hackers. Needless to say, it makes you think hard how can  
this happen.

It turns out Atlanta showed weakness in the area that is quite common to the  
whole networking industry: aged infrastructure that was not patched properly.  
Agreed, sometimes it is hard to keep up with the fast upgrade cycle, but this  
does not justify complacency. Aged software and hardware keeps on being the one  
of the main reasons why security of computer networks is risked on all aspects  
of CIA triad.

Furthermore, it seems that Atlanta had weak or no password policy in place  
which allowed attackers to use brute force attack against the systems. I'm sure  
the research team found other weaknesses as well.

Combining poor life-cycle management with lack of password policy alone is,  
in my opinion, a sufficient evidence of not having a credible security program  
in place. If cities of this size and development are not ready to defend  
themselves, what about the smaller cities and countries of less wealth.  
This is why this incident still gives me the creeps. Atlanta was just  
the first of many.

### Cyber campaign in Ukraine 2015-present

[https://en.wikipedia.org/wiki/December\_2015\_Ukraine\_power\_grid\_cyberattack](https://en.wikipedia.org/wiki/December_2015_Ukraine_power_grid_cyberattack)  
[https://en.wikipedia.org/wiki/2017\_cyberattacks\_on\_Ukraine](https://en.wikipedia.org/wiki/2017_cyberattacks_on_Ukraine)  
[https://blog.talosintelligence.com/2018/05/VPNFilter.html](https://blog.talosintelligence.com/2018/05/VPNFilter.html)  
[https://www.politico.eu/article/ukraine-cyber-war-frontline-russia-malware-attacks/](https://www.politico.eu/article/ukraine-cyber-war-frontline-russia-malware-attacks/)  
[https://www.ft.com/content/8db7251c-1411-11e9-a581-4ff78404524e](https://www.ft.com/content/8db7251c-1411-11e9-a581-4ff78404524e)

The annexation of Crimea conducted by Russia demonstrated a well-orchestrated  
combination of kinetic operations and cyber warfare. Now, it could be that the  
annexation and the cyber incidents in Ukraine happened coincidently at the same  
time because of the country's weakened political posture.

However, I am having a tremendous problem swallowing that. Regardless,  
the combination of the incidents that took place between 2015 and now  
demonstrate an important shift on how modern societies can be disrupted via  
cyber attacks. I cannot recall any other country that has been publicly reported  
being under cyber attacks for such a long period of time. Ukraine has been a  
cyber weapons range for the past five years and no sanctions have been  
handed out. It's scary that cyberspace is a wild west if the actor is suspected  
to be a nation state. Imagine going in IT or any business in Ukraine. You  
basically can't because your information is ok'ed for stealing, business  
operation ok'ed to be disrupted and no cyber insurance will cover your losses  
(ask Mondelez for instance)

In effect, this cyber warfare campaign against Ukraine has made it a no-go-zone  
for modern businesses. The problems have moved from cyber space to kinetic space  
and these attacks have become very real for their victims. Cyber attacks can't  
be treated as a technical problems anymore.

### Mirai aka The Fall of the Internet 2016

[https://en.wikipedia.org/wiki/Mirai\_(malware)](https://en.wikipedia.org/wiki/Mirai_(malware))

Although not an attack to target a single victim, Mirai targeted a specific  
technology. Mirai was the first large attack focused on IoT. Prior to Mirai  
there had been Internet outages due to BGP misconfiguration, submarine  
cable disruptions and, surely, attacks against services such as DNS. Why Mirai  
was an eye opener to me is because it painted a picture of the future ahead of  
us. Almost everything is networked. In few years, we are not going to even use  
the expression such as "I will connect it to Internet". We are connected the  
same way electricity is connected to a fridge: it just is.

Mirai attacked that early on. It did the same thing for IoT as Stuxnet did for  
industrial networks, it exploited the fact that security was not a even an  
after-thought for these products. IoT devices as opposed to typical network  
devices are not designed or procured or even operated by people that have an  
understanding of securing connected environments. Therefore, it was easy for  
Mirai to exploit the default settings these devices came configured with.

It's worrying since majority of procurement departments are unsure how much they  
should pay for security and how much is the right amount of security. If there  
is no interest to pay of security features and hardened configuration baselines,  
there won't be those by default. Our industry must take great measures in  
changing attitudes how products are designed and deployed before incidents such  
as Mirai become a rarity.

On the other hand, Mirai grandly showcased the great weakenesses of Internet.  
What started as a "never-go-down" network has become relic of the past that  
still relies on insecure DNS and BGP protocols. I know, I'm pushing here a l  
ittle bit, but I think that's ok since there has been only incremental  
development to Internet's core technology since the foundation was established.  
Of the protocols BGP is out of Mirai's scope but DNS is not. Put some pressure  
on DNS providers (e.g. Dyn) using DDoS and suddendly you have a large Internet  
outage at hand.

To sum it up connecting any computing platform to any network with a non-harde-  
ned configuration baseline is a cardinal mistake. Make the same mistake too many  
times and we'll have a new Mirai-like incident at hand.

### Value chain breaches back in the day and now

[http://edition.cnn.com/TECH/computing/9909/03/windows.nsa.02/](http://edition.cnn.com/TECH/computing/9909/03/windows.nsa.02/)  
[https://www.ccleaner.com/news/blog/2017/9/18/security-notification-for-ccleaner-v5336162-and-ccleaner-cloud-v1073191-for-32-bit-windows-users](https://www.ccleaner.com/news/blog/2017/9/18/security-notification-for-ccleaner-v5336162-and-ccleaner-cloud-v1073191-for-32-bit-windows-users)

I remember the day well when \_NSAKEY hit the news. I was on the seventh  
grade ('99). One of my buddies came to school all anxious explaining an NSA  
backdoor had been installed to Windows 95 (It was a maths class and we all were  
into computers. It's ok to chuckle.). I knew little back then so I had to  
check the terms backdoor and NSA but as soon as I had ran my Altavista(?)  
searches, I understood not to trust operating systems, implicitly, ever again.

Well-known brands come with some level of in-built trust. As an enterprise or a  
consumer you would like to think that when something is widely adopted it has to  
be good enough to trust implicitly. Value chain attacks exploit this weakness.  
While the perimeter protection on network edges has become good enough in  
general, the next consideration is to find a way to infiltrate malicious code  
in the computers and networks through some other delivery channel instead of  
using network connections. Enter the value chain breaches.

CCleaner in 2017 was a very unfortunate case example of a value chain breach  
completed succesfully by the adversary. In this case the malicous code was  
inserted "at the factory" making it almost impossible for the victims to realize  
they were under attack. Even more so when the data exfiltration from the net  
seemed totally legitimate. Ever since this has been one of the major threats  
big technology companies are focusing on their SDLC program - how to make sure  
someone has not tampered with the original code or the update patches.

I listed this incident here since it hit so many organization in the teeth.  
Trust is earned over years but lost in a single heartbeat. Makes you think  
how long it will take CCleaner to re-establish that trust. For some companies  
this would mean they are out of business for good. Without security there is no  
business to operate.
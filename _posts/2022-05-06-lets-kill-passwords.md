---
layout: post
title:  Let's Kill Passwords
date: 2022-05-06
category: logs
---

Many current and past threats are thriving because of poor passwords or password management practices. In fact, most of our previous threat updates have had some sort of linking to passwords. Let’s skip the reasoning why passwords are like this and move on to what is currently done to alleviate the issue. This week Apple, Google and Microsoft have jointly announced a commitment to speed up extended support for FIDO standard. FIDO works using a challenge signing based on asymmetric encryption (public/private key pair):
 
- a website asks user to login by sending a challenge,
- the user unlocks their authenticator (fingerprint, facial recognition, pressing a button on security key and so on),
- a correct private key is selected and used for signing the challenge
- user’s device sends the signed challenge
- the website verifies the challenge with the user’s registered public key
- the user is logged in.

FIDO log-in removes the problems associated to re-used passwords, managing hundreds of unique passwords per user, forgotten passwords and costs associated to password resets. Combining this to the fact that a lot of infrastructure management relies on Web technology and uses a password login as a sole means of authentication, this announced key-player commitment is great news that should reduce the threat landscape when in action.
 
Log4Shell is still in production in many network and applications. North Korean hackers are exploiting these remaining holes currently. It’s important to patch the systems on time.
 
A new APT group, UNC3524, have been seen to target IP camera surveillance networks. After exploitation, these cameras work as a backdoor to the victims networks. Cameras are often placed exactly on those places that have heightened risk of physical access which makes the cameras themselves susceptible for tampering. Although this new threat focuses on cameras this same commentary could be about any IoT device. This highlights the point why IoT devices should be placed on a protected network of their own.
 
Google’s TAG has confirmed the previous threat update about growing number of threat actors either participating in Ukraine war or benefiting from it. Increasing targeting of critical infrastructure has been observed. War is a theme that makes users more happily open links and files and thus be sucked in to phishing and malware campaigns.
 
TAG reported that a Chinese group called Curios Corge “has remained active against government, military, logistics and manufacturing organizations in Ukraine, Russia and Central Asia.
”. TAG has published the following source IPs as part of their update. They have decided not to publish further details. 
 
Other Chinese APT Bronze President keeps running intelligence campaigns against Russian and European targets. Phishing attacks rely on, e.g., documentation that look like something EU has released and contains information about new sanctions. Files are .exe files but masked to appear as .pdf. When the file is opened a list of other malicious files are downloaded from a remote server.

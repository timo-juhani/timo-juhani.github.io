+++
title = 'Blue Notes'
date = 2024-06-14T20:13:26+03:00
draft = false
+++

Have you ever tried to turn a serial link to a programming interface? Maybe not so listen. While it can be done I'm not sure if it's a good idea.

The common starting for any new network is a bunch of dudes grabbing their blue Cisco console cables and going to work. Although PnP and zero touch provisioning protocols have become more common over the past years we're not totally there yet.

For multiple reasons. Firstly because sometimes the supporting infra (DNS, DHCP you name it) is not there. Secondly there could be a pragmatic reason e.g. don't get it don't use it. Thirdly the tech doesn't support it. Nevertheless the good old blue cable must come out of the back every now and then. 

Enter Tools for the People! 

Our situation might not be optimal but let's try to use what we have. That's why I started this project Blue in which the serial connection is used as an interface over which Python based program enters the device and performs onboarding tasks (v.0.0.1 covers only Cisco SDWAN). This way the stressed out admin can enjoy some degree of industrialization, even offload some of their work to other people and still make sure the job gets done the same way for the 100th time. 

I have to admit that the console link was quite tricky to get working reliably. The main pain points discovered:
- Find a reliable way to return the device to a known prompt. 
- Some commands run for a loooooong time and therefore it's hard to predict how long we should scan for a response on the console. For instance some commands provide a response instantaneously, some don't respond at all and some only create a Syslog message. It's tricky.
- Even if the commands seem to succeed it is a good idea to double check.
- The console link is super slow so for smaller devices therefore delays between commands are a must. In fact, after the bare minimum management configuration has been done it would be a grand idea to switchover to SSH for better reliability.

More to come (maybe, I don't know yet for sure). 

Link to the project: https://github.com/timo-juhani/blue 
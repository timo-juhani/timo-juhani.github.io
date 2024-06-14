+++
title = 'Tools for the People'
date = 2024-06-14T20:03:43+03:00
draft = false
+++

How many times you have worked with networks that are outright insecure, vulnerable and unpatched?

How often do you hear any of these:
a) We don't have time to upgrade.
b) We don't have an SDN or a programmable API to do this.
c) We got hacked because of a known software vulnerability.
d) We just had a major incident because the software base is so old.
e) We didn't know networks need to be patched.

In reality most organizations have massive LAN networks. Therefore all points from a) to e) lead to a huge maintenance debt because they can't keep up with the patching requirements.

The LAN Upgrade project solves the problem by implementing a Python based shell program that automates typical software management tasks required to operate and patch modern Cisco IOS-XE devices.

The design requirements for the project are:
- The program runs over a standard management interface - SSH that is used by if not all then 99% of all organizations globally. Not all are willing to use RESTCONF or NETCONF just yet. That isn't an excuse for fixing the issue. 
- It uses multi-threading to run updates to multiple devices in parallel thus saving time and money.
- To reduce barriers of entry both technical and economical it uses open-source code available through PyPI that anyone can download such as Netmiko.
- It runs on a standard Linux operating system to allow flexible deployment and portability.
- Any user with some networking and Linux shell experience should be able to adopt this tool quickly. For the user it should feel like a no non-sense hacking tool. 
- It doesn't implement any fancy inventory database. Instead is relies on tools that we all know and have available. For inventory that should be either text editor or Excel. That said the inventory file should be trackable by Git.
- All organizations no matter which size or which region should have a basic capability to patch and operate secure networks. This should be fundamental as brushing teeth two times a day. When the foundation is rock solid everything else is easier.

Tools for the people is a full on attack mindset. Instead of getting stuck with complex procurement processes and debate whether a certain tool is worth investing in we move and solve the problem with the tools that are available to all of us. 

Link to the project: https://github.com/timo-juhani/lan_upgrade 
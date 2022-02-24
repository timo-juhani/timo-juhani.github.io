---
layout: post
title:  Hermetic Wiper
date: 2022-02-24
category: logs
---

On Feb 23 Ukraine was hit by a malware attack Hermetic Wiper. First samples claimed to be dating back to January 2022 the malware is a signed driver is used to install a wiper tool that erases files from Windows file systems and corrupts the Master Boot Record for each drive attached. The end result is that the device won't boot up again.  

It may have been that the attacker had taken control over the victims' Microsoft AD in order to drop the driver as a default group policy. It means that all computers managed by the AD were infected.

Protect your ADs. 

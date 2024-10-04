+++
title = 'Cisco SDWAN and Air-Gapped ZTP Server'
date = 2024-10-04T14:11:54+03:00
draft = false
+++

This is a short after action report covering how to deploy an air-gapped ZTP server for Cisco SDWAN. 

The motivation for this log is to fill in the gaps in other documentations and provide a step by step approach how to get to the end goal. 

## 1. Networking

The networking setup is case by case. The goal is to have IP level reachability and make sure that DHCP requests find their target even if their destination is outside their LAN. 

The networking setup used here is the kind where there is a separate staging / install LAN, another LAN for MGMT and shared services such as DNS/DHCP and one for the actual SDWAN underlay on which the VPNs will be deployed. 

````
# The configuration at the gateway.
# Make sure to use helper address if the DHCP/DNS server residers in a different network.

interface GigabitEthernet0/0/0.101
 description SDWAN-INSTALL-GW
 encapsulation dot1Q 101
 ip address 10.1.101.1 255.255.255.0
 ip helper-address 10.1.106.60
````

## 2. DHCP and DNS Server

### Installation

Depending on the DNS/DHCP server you use this step is different. In this installation Dnsmasq was selected. 

Install Dnsmasq using your distro's package manager. If in doubt, duckduckgo.com. 

### Configuring Dnsmasq

The configuration required at Dnsmasq is quite simple for this task. 

The key points to cover you need a DNS server and a DHCP server which provides the domain name with Option 15 and the name server with Option 6. That's it. 

Make sure to tailor the configuration with the right IPs, domain name and interface you are using. 

````
# The concents of /etc/dnsmasq.conf

# Listen to
listen-address=::1,127.0.0.1,10.1.106.60
interface=ens160
port=53

domain-needed
bogus-priv
strict-order
expand-hosts

# Local domain
domain=local.domain

# Upstream servers
server=208.67.222.222 

# DHCP for SDWAN ZTP
dhcp-range=ens160,10.1.101.240,10.1.101.250,24h
dhcp-option=ens160,15,local.domain
dhcp-option=ens160,6,10.1.106.60
````

### Configuring /etc/hosts File

Dnsmasq relies on /etc/hosts file. Therefore, make sure to add the following entries at minimum. 

During ZTP onboarding process the device tries to resolve the ztp.local.domain to find the ZTP server. Without this entry the onboarding can't even start. 

````
# DNS SERVER ITSELF
127.0.0.1 	localhost
127.0.1.1 	bubo
10.1.106.60	bubo

# SDWAN UNDERLAY HOSTS
10.1.100.63	ztp.local.domain
````

### Monitoring

```
# Monitor the DHCP server
tail -f /var/lib/misc/dnsmasq.leases 

# Monitor packets arriving from the installation network
sudo tcpdump net 10.1.101.0/24
```

## 3. ZTP Server

### Installation

Installation media can be downloaded from Cisco.com: https://software.cisco.com/download/home/286320954 

ZTP server uses the same .ova as vBond. Drop that .ova to your ESXi.

Place the first network interface to your OOB portgroup and the second one to SDWAN underlay porgroup. Naturally, use freedom here. The main point is to make sure you have L2 reachability to the right ports from the right segments. 

Once the .ova has deployed login with admin:admin and configure the ZTP server.

### Configuration

The configuration presented here is simplistic. It's all you need to get the setup working. You might want to review the configuration for production environment. For a prototype, though, this is enough.

Make sure to use the same organization name as the rest of the SDWAN infra and use the ztp-server keyword to let the box to know it should work as a ZTP server. VPN 512 is for OOB management and therefore optional. However, it's a small piece of configuration for a big reliability gain. 

Remember to commit your configuration.

````
system
host-name sdwan-ztp-server
system-ip 10.1.106.67
organization-name SPRINT
vbond 10.1.100.63 local ztp-server

vpn 0
interface ge0/0
ip address 10.1.100.63/24
no shutdown
ip route 0.0.0.0/0 10.1.100.1

vpn 512
interface eth0
ip address 10.1.100.65/24
no shutdown
ip route 0.0.0.0/0 10.1.106.1

commit
````

### Device List

The device list is for letting the ZTP know which devices should be allowed to the network. 

Download the list from software.cisco > PnP > Controller Profiles > Download > Provisioning File 

Then just SCP the list from your management box to the ZTP server and install the file.

````
# Install 
request device-upload chassis-file /home/admin/serialFile.viptela

# Confirm
show ztp entries
````

### Certificate

ZTP server certificate must be signed by Cisco. Why? Because the devices that you'll be onboarding with the server come from the factory and don't have a clue about your enterprise CA. 

Other than that you can use your own enterprise CA for the other controllers. 

Pay attention to the point in which the certificate is fixed so that the installation will succeed.

This is how you do it:

````
# Create CSR 
request csr upload home/admin/sdwan-ztp.csr

# Export and copy
request execute cat /home/admin/sdwan-ztp.csr
Copy the CSR

# Upload to Cisco PKI for signing
Go to software.cisco.com > PnP Connect > Certificates > Generate Certificate
Paste the CSR in "Certificate Signing Request" box
Fill the info and press next

# Download the certificate
Press the dowload button from actions column

# Fix the certificate with a text editor
# The downloaded certificate lacks the first and last line of a normal certificate 
-----BEGIN CERTIFICATE-----
<place the downloaded certificate payload here>
-----END CERTIFICATE-----

# Transfer to ZTP server and install the certificate
request certificate install /home/admin/SDWAN-ZTP.cer
````

### Monitor

Remember to run tcpdump on the ZTP server. Ideally, you should eyes on the key servers to make sure all goes well. Especially when you are onboarding the very first few devices. 

````
tcpdump interface ge0/0
````

## 4. Onboard Router 

The process kicks off when you power on a device that doesn't have a startup config. 

Depending on the device type the boot and ZTP process could take a longish time so be patient. 

You should monitor you DHCP/DNS and ZTP servers. The first good sign you'll see is a DHCP lease at Dnsmasq. 

After that move your focus on the Dnsmasq tcpdump and expect to see a whole lot of DNS queries. The one that you're interested in is the record for ztp.local.domain. However, as said this could take a while and before that it's likely you'll see other DNS queries related to other automated onboarding protocols. Be patient. 

When you see ztp.local.domain being resolved take a glance at the ZTP server's tcpdump and you should star to see an increasing amount of communications at UDP port 12346 which is the DTLS control channel. 

If you think you need to start over you can reset the ZTP process on the router using the command below.

````
# A command to reset the configuration and reload the device.
request platform software sdwan software reset
````

Once the onboarding is complete the device should appear to vManage / Manager. But you can check on it via CLI too:

````
show sdwan control local-properties
show sdwan control connections-history
````


## Lesson Learned
- Complex process requires multiple points of monitoring. 
- Sometimes to verify is to only hack and test. 
- Onboarding for certain devices is slow so patience is key. 
- Always have to time to document. It makes no sense someone else to repeat your struggles.
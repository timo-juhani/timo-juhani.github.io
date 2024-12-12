+++
title = 'NSO Cheatsheet'
date = 2024-12-12T08:08:32+02:00
draft = false
+++

The evolving summary of key NSO commands. 

## Installation

### Local

````
# Unpack Linux binary
sh nso-6.0.linux.x86_64.installer.bin --local-install ~/nso-6.0

# Create a NSO instance (local setup)
source ~/nso/ncsrc

# Set env variables
vim .bashrc
source ~/nso-5.8/ncsrc # add to .bashrc
source .bashrc

# Verify NSO is on path
echo $PATH

# Run NCS setup to create a running directory
ncs-setup --dest ~/nso-run

# Or with NEDs
ncs-setup \
--package nso/packages/neds/cisco-ios-cli-6.91 \
--dest nso-run
cd ~/nso-run
````

### NEDs

NEDs are distributed as compressed tar files (.tar.gz). 
NEDs are Cisco NSO packages. Cisco NSO expects to find packages in the packages 
subdirectory of the running directory:
- Do not store anything else in the packages directory.
- Do not keep old packages in the packages directory. 
- You might need to recompile packages that were made for different versions of 
  Cisco NSO.

````
# Upack
sh ncs-6.4-cisco-ios-6.107.2.signed.bin
tar -xvzf ncs-6.4-cisco-ios-6.107.2.tar.gz


# Move
mv cisco-ios-cli-6.107 nso-6.2.8/packages/neds
cd cisco-ios-cli-6.85/src

# Compile
cd nso-6.2.8/packages/neds/cisco-ios-cli-6.107/src
make all

# If need for re-compiling
make clean
make all

# A succesful compilation reports BUILD SUCCESSFUL

# Enter NSO CLI
ncs_cli -u admin -C
packages reload

# Or when NSO is updated launch NSO with 
ncs --with-package-reload

# Verify NED installation
show packages package package-version
````

## Netsim

````
# Create simulated network
ncs-netsim --help
ncs-netsim create-network <NcsPackage > <NumDevices> <Prefix>
ncs-netsim create-network $HOME/neds/cisco-ios-cli-6.85 3 c
ncs-netsim add-to-network neds/cisco-ios-cli-6.85 3 c
ncs-netsim start

# If NSO is already running or has been prior launched import the devices to
# the inventory manually.
ncs-netsim ncs-xml-init > devices.xml

# Enter device configuration mode
ncs-netsim cli-c c1

# Load devices to a running NSO
ncs_load -l -m devices.xml
````

## Launch NSO

```
cd ~/nso-instance
ncs

# Check NSO status
ncs —-status | grep status

# Enter CLI
ncs_cli -u admin -C

# Navigation
config
top 
end
exit
```

## Launch NSO in Project Development Mode

````
ncs-project create test-project
cd test-project

# Use Git!
git init

# Ignore all unimportant files from the repo
printf '*\n!.gitignore' | tee ncs-cdb/.gitignore state/.gitignore logs/.gitignore

# Ignore all make files from the repo
echo "setup.mk*" >> .gitignore

# First commit
git add -A
git commit -m "first commit"
````

## Re-launch NSO

After rebooting the host NSO must be relaunched.

````
# Check NSO status (it's shouldn't be active)
ncs —status | grep status

# Run ncscrc
source ~/nso/ncsrc

# Launch the NSO
cd ~/nso-dev
ncs

# Check the status again
````

## Stop or Reset NSO

````
ncs --stop
ncs-setup --reset
````

## Devices

### Authentication Credentials

````
# Create an authentication group
devices authgroups group labadmin
# Set the credentials
default-map remote-name cisco
default-map remote-password cisco
default-map remote-secondary-password cisco
````

### Add Devices

#### Real Device

````
# Name the device
devices device edge-sw01
# Management IP address 
address 10.10.20.172
# Authentication credentials
authgroup labadmssccin
# NED type
device-type cli ned-id cisco-ios-cli-6.67
# Remote command protocol
device-type cli protocol ssh
# Not the most secure way to go
ssh host-key-verification none
# Consider using SSH keys for authentication

# Ping the device
ping

# Send a connection to the device
connect

# Unlock the device because by default NSO adds them as locked
state admin-state unlocked
````

#### NETSIM Device

````
# Check which ports are used by NETSIM devices
ncs-netsim list

# If using default authgroup login as admin
ncs_cli -C -u admin

# Add a NETSIM device
config
devices device mydevice
address 127.0.0.1 port 10022
device-type cli ned-id cisco-ios protocol ssh
authgroup default
state admin-state unlocked
commit
ssh fetch-host-keys
sync-from
end
````

### Device Synchronization

```
# Check for sync
devices device ios1 check-sync

# Sync CDB from all inventory devices by fetching their configuration
devices sync-from
```

### Device Groups

````
# Use device groups to make working with the inventory more efficient.
# Groups are logical tags.
devices device-group SPOKES
device-name rtr01
device-name rtr02

# Group that captures all devices.
devices device-group ALL
device-group SPOKES
device-group HUBS
<etc>

# Check sync
devices device-group ALL check-sync
````

### View Configuration

````
# Device configuration
show running-config devices device internet-rtr01 config
show running-config devices device internet-rtr01 | de-select config
# Interfaces
show running-config devices device internet-rtr01 config interface
show running-config devices device internet-rtr01 config interface | display json
# Interface IP addresses
# NSO allows using wildcards in commands
show running-config devices device dist-sw01 config interface Vlan * ip address
# XML format
show configuration | display xml
show full-configuration devices device ios1 config | display xml

````

### Update Configuration

````
# Enter device configuration mode
devices device ios0 config
# Verify the path
pwd
# Enter configuration changes and commit

# Configure multiple devices simultaneously
devices device * config ios:enable password magic
````

## Commit Changes

```
# Commit dry-run
commit dry-run outformat native
# Apply changes
commit
```

## Rollback Changes

````
# See the rollback for last configuration update
show configuration rollback changes
# Load the rollback
rollback configuration
# Verify the intended rollback
show configuration
# Verify the commands that will be sent to the device
commit dry-run outformat native
# Rollback
commit
````

## Templates

Devices can be managed individually using NSO as a super-CLI but using templates
multiple devices can be managed at same time using a shared template.

### Create

````
# Create a template using a correct NED
# NOTE: If the template must support multiple OS types just add another ned-id
# and punch in the configuration. 
devices template SET-DNS-SERVER
ned-id cisco-nx-cli-5.20
config
# Add configuration items (OpenDNS servers)
ip name-server servers 208.67.222.222
ip name-server servers 208.67.220.220
# Check on the changes
show configuration
# Save the changes
commit
````

### Apply

````
# List the devices
show devices list
# Apply the template on the target device
devices device dist-sw01 apply-template template-name SET-DNS-SERVER
# Or apply using device group
devices device-group ALL apply-template template-name SET-DNS-SERVER
# Perform the pre-commit SOP
show configuration
commit dry-run outformat native
commit
# If the commit doesn't look good revert the staged changes with
revert
````

## Device Operational State

### Configuration DB

Reads from NSO's configuration DB.

```
show devices device dist-sw01 platform
show devices device dist-sw01 platform serial-number
# Remember that wildcard works
show devices device * platform serial-number
show devices device * platform version
```

### Live Status

Reads directly for the device as it runs.

````
# NED supported commands
show devices device dist-sw01 live-status port-channel
show devices device dist-sw01 live-status ip route

# Raw commands using exec flag
devices device dist-sw01 live-status exec show license usage
devices device dist-sw01 live-status exec any dir
# Pipe the output to a file on the OS
devices device dist* live-status exec show license usage | save /tmp/output.txt
````

### XPath

Finding XPath's can be done using NSO's show commands.

````
# Native 
devices device r1 config interface GigabitEthernet 0/1 ipv4 address | display xpath
show running-config devices device edge-sw01 config vlan | display xpath
# Developer Tools
devtools true
xpath eval /devices/device[name='internet-rtr01']/config/interface/Loopback

````

## Configuration Compliance

Goals:
- Build a device template that contains the desired configuration to test.
- Build a compliance report to check the configuration against a template.

Notes:
- Results support HTML, Bash, or XML format. XML is the default.
- NSO saves the results to a file located in the folder state/compliance-reports
- NSO runs a web server so these reports are available.

````
# Create the device template for compliancy check
devices template COMPLIANCE-CHECK
ned-id cisco-ios-cli-6.67
config
ip name-server name-server-list 208.67.222.222
ip name-server name-server-list 208.67.220.220
service timestamps log datetime localtime show-timezone year
logging host ipv4 10.225.1.11
ntp server peer-list 10.225.1.11

# Create the compliancy check
compliance reports report COMPLIANCE-CHECK
compare-template COMPLIANCE-CHECK ALL
commit

# Run the compliancy check
compliance reports report COMPLIANCE-CHECK run
# And define outformat if needed (text, html, xml)
compliance reports report COMPLIANCE-CHECK run outformat text

# Resolve the compliancy issues by applying the template to devices.
````

## Services

Service is a configuration engine that uses templates and variables to render 
configurations that can be repeatedly applied. Services are divided to customer 
facing and resource facing. The former is what is exposed to the user and the 
latter is called and executed against the device. 

### Create a Service Package

NSO uses YANG to define the variable names and constraints on those variables.
NSO uses XML behind the scenes to represent every device's configuration.

```
cd nso-instance/packages/
ls
ncs-make-package -h

# Template used with static variables.
ncs-make-package --service-skeleton template loopback-service

# Python & template used when dynamic variables are required.
ncs-make-package --service-skeleton python-and-template svi
```

### Create YANG Model

````
# Confirm the model is correct. 
# NOTE: This may produce a long list of errors not related to the package being
# developed. Those can be ignored.
pyang looback.yang

# Make 
cd ~/nso-run/packages/loopback/src 
make

# Reload package in NSO
# Required every time there is a change in YANG model 
packages reload
````

### Create XML Configuration Template

SOP Alpha: Use NSO (NED) device config mode to create the XML.

````
show configuration
commit dry-run outformat xml
````

SOP Bravo: Use NSO to pull native device configuration in XML format. 

```
show full-configuration devices device ios1 config | display xml
```

Copy text between config tags only and paste it to the .xml template file 
created during ncs-make-package.

Add variable tags to the template and append YANG variables with each variable
tag and data type. Parameter names must match those modeled with YANG.

````
# Static variable
<secret>{/secret}</secret>

# Python based variable
<ip>{$IP-ADDR}</ip>
````

Exit NSO session and then:

```
cd loopback-service/src/
make
```

Enter NSO and reload packages.

```
ncs_cli -C -u admin
packages reload
```


### Create a Service Instance

```
# Create a customer if it's not there already
config
customers customer ACME
```

```
# General flow example
# Configure a service on a device
simple-service test1 device ios0 secret mypasswd
```

````
# L3VPN example
services l3vpn ACME vpn-id 10001
customer ACME
link 2 link-name Site1 pe-device PE11 interface 0/1 routing-protocol bgp neighbor 172.16.1.1
exit

link 3 link-name Site3 pe-device PE31 interface 0/1 routing-protocol static
static-route 192.168.1.0 mask 255.255.255.0
exit
static-route 192.168.2.0 mask 255.255.255.0
````

````
# Check the current running config of a service in table format
show running-config services l3vpn | tab

# And in XPATH
show running-config services l3vpn ACME | display xpath
````

### Service Instance Management

````
# Checks the sync from NSO CDB
services l3mplsvpn S1 check-sync

# Checks the sync from device itself (remote)
services l3mplsvpn S1 deep-check-sync

# Redeploy a service if changes were made manually after deploying the service
simple-service test1 re-deploy

# Undeploy from devices but keep in NSO CDB
simple-service test1 un-deploy

# Delete the service
no simple-service test1

# Get service modifications
simple-service test1 get-modifications

# Compare device configuration to configuration in NSO CDB
simple-service test1 compare-config
````

````
# Debug Python code
tail -n 20 ~/nso-run/logs/ncs-python-vm-svi.log
````
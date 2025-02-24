+++
title = 'VXLAN in Data Centers'
date = 2025-02-24T08:34:36+02:00
draft = false
+++

## Quick Refresher
The main idea behind VXLAN in data centers is the ability to move workloads between the servers without changing the network settings and/or shutting down or restarting the workload. The business need for this capability is rooted in the reasons found from on-demand, elastic scalability, business continuity, or maintenance requirements.

VXLAN is a tunneling protocol which encapsulates the original payload and thus creates separation from the other traffic flows. The concept is similar to what VRF-based technologies implement. The tunnels start and end at VXLAN Tunnel Endpoints or VTEPs.

VTEP does the encapsulation of the traffic. A VTEP comes with two interfaces. One for local bridging purposes and one to provide an IP interface towards the VXLAN core network. Depending on the case, you can see VTEPs deployed on physical switches, hypervisors, or directly on servers' operating systems. Having all the key deployment options available should give an idea of the flexibility in setting up the tunnel infrastructure.

So if VTEPs are the core concept in the forwarding, how does the network learn and distribute the information about VTEPs? The two most common options are MP-BGP EVPN and flooding (multicast) control plane solutions. The forwarding decision is based on the destination MAC address. If the MAC is not local to the VTEP, the packet will be encapsulated in a VXLAN header and sent to the remote VTEP over the VXLAN core network. If the MAC is local to the VTEP, the traffic is switched just normally without VXLAN.

Time to get hands on. 

## Topology

Typical CLOS topology. 

[](/images/clos-fabric-bgp.png)

## Configuration

Since we're dealing with standalone NX-OS devices, let's split the configuration into manageable tasks that follow a logical order.

### 1. Underlay

For the underlay, we need a simple OSPF setup that provides inter-loopback connectivity within the fabric. The links are point-to-point, so we don't need the OSPF DR process to run.

In addition to OSPF, multicast is required to handle BUM traffic replication in the underlay. Without multicast support, VTEPs wouldn't know where to send BUM traffic. Later in the configuration, we'll map VNIs to multicast groups, and VTEPs will join those groups using IGMP. This setup ensures that the underlay handles BUM traffic efficiently.

An alternative to multicast is ingress replication, also known as headend replication. This option can simplify the configuration by eliminating the need for a multicast underlay. However, headend replication could become a performance issue in larger networks since each VTEP replicates packets. Keep this in mind.

There are other alternatives, but they may prove impractical in real-life scenarios.

Back to business: We'll go with multicast, and the first step is setting up the PIM infrastructure in the underlay.

#### Spines

````
feature ospf
feature pim

ip pim rp-address 1.0.0.1
ip pim rp-address 1.0.0.2

interface Loopback0
  description CORE-LOOPBACK
  ip address 1.0.0.1/32 
  ip router ospf 1 area 0
  ip pim sparse-mode

interface Ethernet1/1
  description LEAF-1
  no switchport
  mtu 9216
  ip address 10.0.1.1/30
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  description LEAF-2
  no switchport
  mtu 9216
  ip address 10.0.1.5/30
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
  
router ospf 1
  router-id 1.0.0.1
````

#### Leafs

`````
feature ospf
feature pim

ip pim rp-address 1.0.0.1
ip pim rp-address 1.0.0.2

interface Loopback0
  description CORE-LOOPBACK
  ip address 1.0.0.3/32
  ip router ospf 1 area 0
  ip pim sparse-mode

interface Ethernet1/1
  description SPINE-1
  no switchport
  mtu 9216
  ip address 10.0.1.2/30
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  description SPINE-2
  no switchport
  mtu 9216
  ip address 10.0.2.2/30
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

router ospf 1
  router-id 1.0.0.3
`````
### 2. Enable VXLAN and BGP EVPN

Enable the required features on all switches.

````
feature nv overlay
feature bgp
nv overlay evpn
````

### 3. BGP EVPN

BGP EVPN is implemented to provide controlled learning of the MAC addresses across the fabric. This way, learning by flooding can be largely avoided and replaced with a more scalable approach. Although flood and learn can be used in very limited solutions, all production-grade networks should implement BGP EVPN.

#### Spines

Pay attention to the AS number selection in combination with the update source. This type of configuration requires increasing the default BGP TTL value (ebgp-multihop).

````
router bgp 65000
  router-id 1.0.0.1

  neighbor 1.0.0.3 remote-as 65003
    description LEAF-1
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community extended

  neighbor 1.0.0.4 remote-as 65004
    description LEAF-2
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community extended
````

#### Leafs

Allocate a unique AS number per leaf to avoid having to bypass the default BGP loop prevention mechanisms. 

````
router bgp 65003
  router-id 1.0.0.3

  address-family l2vpn evpn
    retain route-target all

  neighbor 1.0.0.1 remote-as 65000
    description SPINE-1
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community extended

  neighbor 1.0.0.2 remote-as 65000
    description SPINE-2
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community extended
````

### 4. Configure VXLAN and VNI Mappings on Leafs

Map the local VLAN to a virtual network (VNI) and create an interface for it. The interface configuration uses Loopback0 as the source which is the VTEP in the underlay. Multicast group is assigned for BUM replication. 

This virtual network will be a L2 VNI and thus the forwarding relies on MAC learning.

````
feature vn-segment-vlan-based

vlan 100
  vn-segment 10000

interface nve1
  no shutdown
  source-interface loopback0
  member vni 10000
    mcast-group 239.1.1.100

evpn
  vni 10000 l2
    rd auto
    route-target import auto
    route-target export auto
````

### 5. Attach VLANs to Leaf Ports

Adding a server port is simple as creating an access port. 

`````
interface Ethernet1/3
  description SERVER-1
  switchport access vlan 100
  spanning-tree port type edge
  spanning-tree bpduguard enable
  mtu 9216
`````

### Validate

Validation can be done by using the commands below. 

When running ping tests remember that we can only implemented a L2 network that doens't have a gateway functionality yet. So there is no connectivity to external networks. 

`````
show ip ospf neighbor
show ip pim neighbor
show ip pim rp
show ip mroute
show bgp l2vpn evpn summary
show nve peers
show mach address-table
`````

## Lessons

- This isn't as hard as they tell you, as long as you keep the network configuration patterns standardized.
- Automation and management tools become handy as you expand virtual networks, add new servers, and connect new leafs.
- The security policy plane requires extra attention. It's not included here, and you might need to consider implementing it on host operating systems, virtual switches, and firewalls.
- Some massive platform companies have opted to use pure L3 BGP fabric design (the topic of the previous log) instead of VXLAN overlays as discussed in this log.
- There are good reasons for that:
    - Scaling up: It works for internet-scale networks.
    - Simplicity: No complexity from VXLAN.
    - Control: BGP's native routing policy controls.
    - Ease of automation: It's not bad for VXLAN either.
    - East-west traffic: The optimization goal is the fastest route between switches, which BGP does just fine. There is no need to stretch L2 or do segmentation between different tenants, which are some of VXLAN's key points.
    - Cost: Pure L3 BGP is more cost-efficient due to its simplicity.
    - Efficiency: No overhead from VXLAN headers and no multicast complexity.
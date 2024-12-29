---
title: Network Terminologies
fraft: false
---

- [Open Systems Interconnection model vs. TCP/IP model](#open-systems-interconnection-model-vs-tcpip-model)
- [Layer 2 And Layer 3 Switches In Networking System](#layer-2-and-layer-3-switches-in-networking-system)
- [Internet Protocol (IP) Addresses](#internet-protocol-ip-addresses)
  - [IPv4 addresses and mask](#ipv4-addresses-and-mask)
  - [IPv6 addresses](#ipv6-addresses)
  - [Reserved IP addresses](#reserved-ip-addresses)
- [Most common ports (/etc/services)](#most-common-ports-etcservices)
  - [ARP (Address Resolution Protocol)](#arp-address-resolution-protocol)
    - [ARP Process](#arp-process)
      - [ARP Request](#arp-request)
      - [ARP Response](#arp-response)
      - [ARP Timing](#arp-timing)
    - [Proxy ARP](#proxy-arp)
      - [Gratuitous ARP](#gratuitous-arp)
        - [Updating ARP Mapping](#updating-arp-mapping)
        - [Announcing a Node’s Existence](#announcing-a-nodes-existence)
        - [Redundancy](#redundancy)
        - [Redundant IP Addresses](#redundant-ip-addresses)
        - [Redundant IP and MAC Addresses](#redundant-ip-and-mac-addresses)
      - [ARP Probe and ARP Announcement](#arp-probe-and-arp-announcement)
    - [ARP Linux command](#arp-linux-command)
  - [RARP (Reverse Address Resolution Protocol)](#rarp-reverse-address-resolution-protocol)
  - [Security Enhanced Linux (SELinux)](#security-enhanced-linux-selinux)
  - [Show/manipulate traffic control settings](#showmanipulate-traffic-control-settings)
  - [Virtual Private Network (OpenVPN)](#virtual-private-network-openvpn)
  - [E-mail](#e-mail)
- [Teaming vs Bonding in Redhat](#teaming-vs-bonding-in-redhat)
  - [Migration](#migration)
  - [Link Aggregration](#link-aggregration)
    - [Limitations](#limitations)
    - [Common link aggregation terminology](#common-link-aggregation-terminology)
- [Virtual Local Area Networks (VLANs)](#virtual-local-area-networks-vlans)
  - [Tagged Ports and Untagged Ports](#tagged-ports-and-untagged-ports)
  - [Access Ports and End-Host Devices](#access-ports-and-end-host-devices)
  - [Terminology](#terminology)
  - [802.1q VLAN Tag](#8021q-vlan-tag)
  - [Native VLAN](#native-vlan)
- [Routing Between VLANs (inter-vlan routing / Router on a Stick (RoaS))](#routing-between-vlans-inter-vlan-routing--router-on-a-stick-roas)
  - [Router with Separate Physical Interfaces](#router-with-separate-physical-interfaces)
    - [Router with Sub-Interfaces](#router-with-sub-interfaces)
    - [Layer 3 Switch](#layer-3-switch)
- [Promiscuous Mode](#promiscuous-mode)

# Open Systems Interconnection model vs. TCP/IP model

“**P**eople **D**on't **N**eed **T**hose **S**tupid **P**ackets **A**nymore!”

| OSI Layer       | Data     | Protocols                                                 | TCP/IP Layer   |
| :-------------- | :------- | :-------------------------------------------------------- | :------------- |
| 7. Application  | Data     | Data generation (SMTP, NNTP, SSH, Telnet, HTTP)           | Application    |
| 6. Presentation | Data     | Encryption and formatting (JPEG, ASCII, EBDIC, GIF,…)     | Application    |
| 5. Session      | Data     | Sync. & send to ports (RPC, SQL, NFS, NetBIOS)            | Application    |
| 4. Transport    | Segments | TCP/UDP, message segmentation, message traffic control    | Transport      |
| 3. Network      | Packets  | Packets, IP addr., routing, subnet traffic (IPv4/6, ICMP) | Network        |
| 2. Data Link    | Frames   | Frame traffic control, sequencing (ARP, MAC)              | Network Access |
| 1. Physical     | bits     | Cables, hubs, physical medium transmission                | Network Access |

<https://www.softwaretestinghelp.com/osi-model-layers/>

- Top Layer – Application Layer
  - Provision user interface using protocol: FTP, HTTP
  - will communicate with the end users & user applications.

- Layer 6 – Presentation Layer
  - Present data and handles data encryption
  - It plays the role of a translator so that the two systems come on the same platform for communication and will easily understand each other.
  - The data which is in the form of characters and numbers are split into bits before transmission by the layer. It translates the data for networks in the form in which they require it and for devices like phones, PC, etc in the format they require it.
  - The layer also performs data encryption at the sender’s end and data decryption at the receiver’s end.
  - It also performs data compression for multimedia data before transmitting, as the length of multimedia data is very big and much bandwidth will be required to transmit it over media, this data is compressed into small packets and at the receiver’s end, it will be decompressed to get the original length of data in its own format.

- 5 Session Layer
  
  Keeps different applications data separate and provides synchronisation

# Layer 2 And Layer 3 Switches In Networking System

# Internet Protocol (IP) Addresses

## IPv4 addresses and mask

| CIDR Notation:     | 192.168.1.130/25   |                                     |
| :----------------- | :----------------- | :---------------------------------- |
| IPv4 (32bit):      | 192.168.1.130      | 11000000.10101000.00000001.10000010 |
| Mask:              | 255.255.255.128    | 11111111.11111111.11111111.10000000 |
| Subnet:            | ( IP and Mask )    | 11000000.10101000.00000001.10000000 |
| Subnet:            | 192.168.1.128      |                                     |
| Usable Host Range: | 192.168.1.129--254 |                                     |
| Broadcast Address: | 192.168.1.255      |                                     |

## IPv6 addresses

- IPv6 (128bit) – y : y : y : y : y : y : y : y
- IPv6 with IPv4 part – y : y : y : y : y : y : x . x . x . x
- Access IPv6 URL – <http://[2a01:4f8:130:2192::2>]
- Zeroes can be ommitted:
  - Loopback: `00000:0000:0000:0000:000:0000:0000:0001`  ≈  `0:0:0:0:0:0:0:1`  ≈  `::1`
  - Multiple zero groups “::” : `2001:0:0:0:0:0:0:ab`  ≈  `2001::ab`
  - Use only one groups symbol: `2001:0:0:1:0:0:0:ab`  ≈  `2001:0:0:1::ab`  ≈  `2001::1:0:0:0:ab`
- IEEE EUI-64 identifier:
  1. Ethernet (Media Access Control) address: 38:c9:86:30:63:bf
  2. Identificator: 0 local, 1 global.
  3. EUI-64 identifier: (38 or identificator = 39) 3ac9:86ff:fe30:63bf
  4. Link-local address: fe80::3ac9:86ff:fe30:63bf

## Reserved IP addresses

- Loopback: 127.0.0.1/8; ::1/128
- Unspecified address: 0.0.0.0/8; ::/128
- Multicast: 224.0.0.0/4; ff00::/8
- Private: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16; fc00::/7
- Automatic Private IP: 169.254.0.0/16
- IPv4 mapped addresses: ::ffff:0:0/96 (::ffff:0.0.0.0 – ::ffff:255.255.255.255)
- IPv4/IPv6 translation: 64:ff9b::/96
- For documentation examples: 192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24; 2001:db8::/32

# Most common ports (/etc/services)

| Port Types       | Range          |
| :--------------- | :------------- |
| Well known Ports | 0 – 1023       |
| Registered Ports | 1024 – 49151   |
| Ephemeral Ports  | 49152 – 65535  |

> Privilege port < 1024 can be opened only by the root user!

|                       |                                 |                         |                                 |
| :-------------------- | :------------------------------ | :---------------------- | :------------------------------ |
| 20, 21 FTP            | 88  Kerberos                    | 143 IMAP                | 1080 Socks5                     |
| 22 SSH (Secure Shell) | 110 POP3                        | 161, 162  SNMP          | 1194 OpenVPN                    |
| 23 Telnet             | 111 Portmapper – Linux          | 389 LDAP                | 1241 Nessus Server              |
| 25 SMTP               | 119 NNTP (Network News)         | 443 HTTPS (HTTP Secure) | 1433, 1434  SQL Server          |
| 42 WINS               | 123 NTP (Network Time Protocol) | 445 CIFS                | 1494, 2598  Citrix Applications |
| 53  DNS               | 135 RPC-DCOM                    | 514 Syslog              | 1521 Oracle Listener            |
| 80, 8080 HTTP         | 139 SMB                         | 636 Secure LDAP         | 2512, 2513  Citrix Management   |

## ARP (Address Resolution Protocol)

It is used to convert an IP address to its corresponding physical address(i.e., MAC Address). ARP is used by the Data Link Layer to identify the MAC address of the Receiver’s machine.
Address Resolution Protocol (ARP) is the process by which a known L3 address is mapped to an unknown L2 address. The purpose for creating such a mapping is so a packet’s L2 header can be properly populated to deliver a packet to the next NIC in the path between two end points.

The “next NIC” in the path will become the target of the ARP request.

If a host is speaking to another host on the same IP network, the target for the ARP request is the other host’s IP address. If a host is speaking to another host on a different IP network, the target for the ARP request will be the Default Gateway’s IP address.

In the same way, if a Router is delivering a packet to the destination host, the Router’s ARP target will be the Host’s IP address. If a Router is delivering a packet to the next Router in the path to the host, the ARP target will be the other Router’s Interface IP address – as indicated by the relative entry in the Routing table.

The same functionality exists for IPv6 networks in the Neighbor Discovery Protocol (NDP)

### ARP Process

The Address Resolution itself is a two step process – a request and a response.

It starts with the initiator sending an ARP Request as a broadcast frame to the entire network. This request must be a broadcast, because at this point the initiator does not know the target’s MAC address, and is therefore unable to send a unicast frame to the target.

Since it was a broadcast, all nodes on the network will receive the ARP Request. All nodes will take a look at the content of the ARP request to determine whether they are the intended target. The nodes which are not the intended target will silently discard the packet.

The node which is the target of the ARP Request will then send an ARP Response back to the original sender. Since the target knows who sent the initial ARP Request, it is able to send the ARP Response unicast, directly back to the initiator.

#### ARP Request

The ARP Request is an ARP payload carried within the appropriate L2 header for the medium in use. The majority of the time this will be Ethernet.
The Ethernet header will include three fields: a Destination MAC address, a Source MAC address, and an EtherType.

Notice

- the Layer 2 Destination is ffff.ffff.ffff, this is the special reserved MAC address indicating a broadcast frame. This is what makes an ARP Request a broadcast.
- The EtherType contains the hex value 0x0806, which is the reserved EtherType for Address Resolution packets.
- This particular Ethernet header also includes some padding. The size of the Destination/Source/Type fields is 14 bytes, the size of the ensuing ARP Payload is 28 bytes, and the size of the trailing FCS (Frame Check Sequence) is 4 bytes. Which means an additional 18 bytes of padding had to be added to ensure this frame reaches the minimum acceptable length of 64 bytes.
- The ARP payload itself has a few fields
  - The Hardware Type and Protocol Type fields indicate what type of addresses are being mapped to each other
  - The Hardware Size and Protocol Size refer to the amount of bytes in each of the aforementioned types of addresses: a MAC address is 6 bytes (or 48 bits), and an IPv4 address is 4 bytes (or 32 bits).
  - The Opcode indicates what type of ARP packet this is. There are really only two values you will see. A value of 1 indicates this ARP packet is a Request, or a value of 2 would indicates this ARP packet is a Response
  - The Sender MAC address and Sender IP address
  - The Target MAC address and Target IP address refer to intended target of the ARP Request . the Target IP address is filled in, but the Target MAC address is all zeros as it does not know it yet

-

#### ARP Response

The ARP Response has a very similar packet structure but the Destination MAC will be of the  initial requester and Source mac will be of the original target and Opscode set to 2. As the frame is addressed directly back to initial requester – this is what makes the request unicast.

#### ARP Timing

When the ARP process completes, the information learned is stored in an ARP Table, or ARP Cache. Every device that has an IP address maintains such a table. Entries in this table expire after certain a duration.

Typically, for clients and end hosts, the ARP timeout will be pretty short – typically 60 seconds or less. The reason for this is at the client level, lots of mobility and movement happens, and you don’t want to cache an ARP entry for a very long time

Whereas for network infrastructure devices (routers, firewalls, etc), the ARP timeout is very long – typically 2-4 hours. The reason for this is at the infrastructure level, the nodes joining and leaving the network should remain pretty consistent. A router is added to the network when it is initially built out, than occasionally as the network scales. Whereas hosts will be added and removed to your network on a day by day basis.

Of course, a Router has no way of knowing whether an entry in its ARP Cache is a host or another infrastructure device. Instead, to keep up with the mobility of clients, a Router’s cache is updated whenever it receives an ARP request.

Which is to say, if a Host is refreshing its default gateway’s ARP mapping every 30~ seconds due to the host’s shorter ARP timing, the default gateway’s ARP mapping of that same host will also be updated every 30 seconds.

There are also other strategies that exist that serve the purpose of keeping an ARP mapping up to date.

### Proxy ARP

Proxy ARP occurs when one node is responding to an ARP request on behalf of another node. Proxy ARP is not a malicious event, it occurs to enable connectivity between two hosts that wouldn’t otherwise be possible.

The original thought process for Proxy ARP was to accommodate hosts with misconfigured subnet masks.

when a host is speaking to another host on the same IP network, the target for the ARP request is the other host’s IP address. If a host is speaking to another host on a different IP network, the target for the ARP request will be the Default Gateway’s IP address.

The item which tells a host whether another IP address is on the same network or a different network is the subnet mask.

Example:
Host A is configured with the IP address 10.0.0.11 and a subnet mask of 255.255.255.0 (or /24 in CIDR). Host A will consider any IP address in the range of 10.0.0.0 – 10.0.0.255 on its local network.

Host B is configured with the IP address 10.0.0.22 and misconfigured with a subnet mask of 255.255.0.0 (or /16 in CIDR). Host B will consider any IP address in the range of 10.0.0.0 – 10.0.255.255 on its local network.

Presume both of these hosts are trying to speak to Host D, which exists on a different network and has the IP address 10.0.4.44.

When Host A tries to speak to 10.0.4.44, it would (correctly) consider Host D on a different network and would use traditional ARP to send the packet to the default gateway.

However, when Host B tries to speak to 10.0.4.44, it would (incorrectly) consider Host D on the same network and would instead try to ARP for Host D’s MAC address directly.

Host B’s ARP Request will be broadcast to the local network, but will never make it across the Router to Host D. Therefore, the ARP Request will go unanswered, and Host B will be unable to communicate with Host D.

Unless the Router itself responds to Host B’s ARP Request on behalf of Host D – which is the exact definition of a Proxy ARP.

The ARP Response sent by the Router looks exactly like a normal ARP response. Host B will use the response to create an ARP mapping that states the IP 10.0.4.44 maps to the MAC address 0053.ffff.9999. All subsequent packets sent to Host D will use this MAC address in the L2 header.

So despite the misconfigured Subnet Mask, Host B will be able to speak to Host D, due to the Router’s heroic use of Proxy ARP.
That being said, it does impose additional work load on the Router. We used the specific example of Host D’s single IP address, but due to Host B’s misconfigured subnet mask there are roughly 65,000 IP addresses that Host B now considers on its local network. When in reality only about 250 could possibly exist on its local network

So while Proxy ARP enabled connectivity in this example, it unfortunately does not scale indefinitely and should not be relied upon. The long term solution is to correct the misconfigured Subnet mask on Host B.

Moreover, this specific use case for Proxy ARP is based solely around enabling routing despite there being a misconfiguration. With Proxy ARP, Host B may never even realize it has an incorrect Subnet Mask.

There is another school of thought that instead would prefer Host B’s misconfiguration to cause the communication to fail in order for Host B to be notified that something is wrong. Thereby creating an opportunity for Host B to fix the root problem.

Many routers these days do not send Proxy ARPs by default for this very reason. Although most include a way to enable it if desired.

However, there is a very legitimate and necessary use case for Proxy ARP, and that has to do with Network Address Translation, or NAT. Firewall configure with static NAT will use Proxy ARP to respond to Router ARP Request to translate the Target IP address to an internal IP address

There are ways around requiring Proxy ARP on a NAT device, but they involves using Static Routes on the upstream Router to manually instruct the Router to send packets which must be translated to the Firewall’s interface. While viable, this solution does not scale

#### Gratuitous ARP

A Gratuitous ARP is an ARP Response that was not prompted by an ARP Request. The Gratuitous ARP is sent as a broadcast, as a way for a node to announce or update its IP to MAC mapping to the entire network.

The Opcode is set to 2, indicating a response. Despite the fact that this packet did not actually follow a request. This is why it is said that a Gratuitous ARP is broadcast ARP Response, that was not prompted by an ARP Request.

the Target IP address will be same as the source IP address.

Gratuitous ARP Use Cases

- Updating ARP Mapping
- Announcing a Node’s Existence
- Redundancy
- Redundant IP Addresses
- Redundant IP and MAC Addresses

##### Updating ARP Mapping

The first use case is pretty straight forward, a node can use a Gratuitous ARP to update the ARP mapping of the other hosts on the network should the node’s IP to MAC mapping change.

This might happen if a user manually modifies their MAC address – they retain the same IP address, but now have a new MAC address. Therefore, the ARP mapping for all the nodes which are communicating with this user must be updated.

That being said, manually changing the MAC address is pretty rare. However, you do sometimes see this in redundant Cloud or Virtual environments, where a particular Virtual Machine (VM) ‘jumps’ to a new physical box – the same VM’s IP address is now being served by a different physical machine.

##### Announcing a Node’s Existence

Announcing a Node’s Existence
The second use case for Gratuitous ARP is when a host newly joins a network — it can use Gratuitous ARP to announce it’s existence to the network.

The intent motivating this action is useful — it is an attempt to preemptively populate ARP caches of neighboring hosts without requiring them to initiate the Traditional ARP process.

However, there is no mandate for hosts to cache ARP mappings in every Gratuitous ARP they receive. As a result, this use case provides little benefit. It causes no significant harm though, so this behavior is not discouraged.

##### Redundancy

Much more substantial is Gratuitous ARP’s use case in situations where redundancy or failover between two devices are used.

With redundancy, you typically have two scenarios: two devices sharing an IP address, but each having their own MAC address. Or, two devices sharing both an IP address and a MAC address.

In both of these cases, Gratuitous ARP is critically important to ensure the continued ability to communicate with the IP address as it shifts between the two redundant devices. Below are examples of each scenario and how Gratuitous ARP is employed.

##### Redundant IP Addresses

In this scenario, only the IP address is redundant. Two devices will share a single IP address, but each device has their own unique MAC addresses.

Our example will use two Routers sharing the IP address 10.0.0.1. The hosts in our example will be using this shared IP address as their default gateway.

When one of the routers experiences a failure, the other router sends a Gratuitous ARP.

The specific contents of the Gratuitous ARP match the packet structure described above, but the essential purpose of the packet is to let everyone on the network know that the IP 10.0.0.1 is now being served by the other router’s MAC address.

Upon receiving the Gratuitous ARP, all the hosts update their ARP tables with the new mapping so they can continue to send traffic to their default gateway IP address through the non-failed Router.

The process continues indefinitely in the case of the opposite router failing.

##### Redundant IP and MAC Addresses

In this scenario, both the IP address and the MAC address are redundant. Two devices will share both an IP address and a MAC address.

Our example will again use two Routers, but this time they will be sharing the IP address 10.0.0.1 and the MAC address 0053.ffff.1111.

The hosts will still use the shared IP address as their default gateway, but in this example their ARP mapping will never need to change – the shared IP address 10.0.0.1 will always map to the shared MAC address 0053.ffff.1111.

Despite the hosts’ ARP mapping never needing to be updated, Gratuitous ARP is still crucial. But not for the sake of updating the hosts’ ARP mapping, but for the sake of updating the switch’s MAC address table so the shared MAC address is associated with the correct port. Recall, switches learn MAC address mappings from the Source MAC address of any received frame.

As a router fails, the other router sends a Gratuitous ARP. The switch then updates its MAC address table with the new location of the device that owns the shared MAC address. In this way, frames sent by the hosts addressed to 0053.ffff.1111 will always egress the correct switch port.

First Hop Redundancy Protocols (FHRPs) like HSRP or VRRP use this exact strategy to enable both routers to share the same IP address and MAC address. Gratuitous ARP is instrumental to enable this type of functionality.

#### ARP Probe and ARP Announcement

It is beneficial for a host to first test an IP address before putting it to use to ensure it is indeed unique. One such way of determining if an IP address in use is to use ARP. Or specifically, an ARP Probe.

### ARP Linux command

The arp command allows users to manipulate the neighbor cache or ARP table. It is contained in the Net-tools package along with many other notable networking commands (such as ifconfig). The arp command has since been replaced by the ip neighbour command.

|                                                  |                                                            |
| :----------------------------------------------- | :--------------------------------------------------------- |
| arp                                              | lists the current contents of the ARP cache.               |
| arp -i bondX                                     | Display entries for a specific interface                   |
| arp -a 192.168.0.1                               | Display entries for a specific addres                      |
| arp -s 192.168.0.1 -i ethX 51:53:00:17:34:09     | add an entry (permanently) to the cache, use the -s option |
| arp -d 192.168.0.1                               | To remove an entry from the arp cache                      |
| ip neigh show                                    | To display the current entries in the arp table,           |
| ip neigh add 192.168.0.1 dev ethX                | To add a new entry to the arp table                        |
| ip neigh del 192.168.0.1 dev ethX                | To delete an existing entry from the table                 |
| ifconfig -arp eth0; ip link set dev eth0 arp off | Switch ARP resolution off on one device                    |
| arpping -I interface -c count destination        |                                                            |
| ip -s neigh flush all                            | Clean ARP table                                            |
| arp; ip neighbor list; cat /proc/net/arp         | Show ARP table                                             |

Some useful flags are:

- C: Complete entry.
- M: Permanent entry.
- P: Published entry.

## RARP (Reverse Address Resolution Protocol)

It provides the IP address of the device given a physical address as input. But RARP has become obsolete since the time DHCP has come into the picture.

## Security Enhanced Linux (SELinux)

- List port mapping: semanage port -l
- Use 8000 for http: semanage port -a -t http_port_t -p tcp 8000
- Check status: getenforce
- Disable SELinux temporarily: setenforce 0
- Set directory accessible by httpd: chcon -R -t httpd_sys_content_t ./directory

## Show/manipulate traffic control settings

- List existing rules: tc -s qdisc ls dev eth0
- Slow down traffic by 200 ms: tc qdisc add dev eth0 root netem delay 200ms
- Delete all rules: tc qdisc del dev eth0 root

## Virtual Private Network (OpenVPN)

- TUN device for IP traffic, TAP device for ethernet frames
- Enable UDP port 1194: iptables -A INPUT -i eth0 -m state --state NEW -p udp --dport 1194 -j ACCEPT, firewall-cmd --permanent --add-service openvpn
- Basic server: openvpn --ifconfig 10.200.0.1 10.200.0.2 --dev tun
- Basic client: openvpn --ifconfig 10.200.0.2 10.200.0.1 --dev tun --remote your.openvpnserver.net
- Use TCP protocol: --proto tcp-server (server), --proto tcp-client (client)
- Create/use static key: openvpn --genkey --secret secret.key and use --secret secret.key on client/server.

## E-mail

- Send email: curl --mail-from blah@test.com --mail-rcpt foo@test.com smtp://mailserver.com
- Send email: mail -s "This is subject" foo@test.com

# Teaming vs Bonding in Redhat

Teaming was introduced in RHEL7 and have been redesigned with flexibility in mind, more features, and work great with network manager. RedHat (RHEL7) still support Bonding for backward compatibility but it's expected to be replaced by Teaming in later releases.

Red Hat Enterprise Linux has, for some time, provided users with a bonding driver to achieve link aggregation. In fact, bonding works well for most applications. That said, the bonding driver's architecture is such that the control, management, and data paths are all managed in the kernel space... limiting its flexibility.

The team driver is not trying to replicate or mimic the bonding driver, it has actually been designed to solve the same problem(s) using a wholly different design and different approach; an approach where special attention was paid to flexibility and efficiency. The best part is that the configuration, management, and monitoring of team driver is significantly improved with no compromise on performance, features, or throughput.

## Migration

To facilitate migration from bonding driver to team driver we have created a robust migration script called bond2team. Please see manual pages of bond2team (man 1 bond2team) for available options.  In essence this script allows existing deployments of bonded interfaces to be moved to teamed interfaces seamlessly.

Host requirements for link aggregation (etherchannel, port channel, or LACP)

## Link Aggregration

### Limitations

- Link aggregation is never supported on disparate trunked switches.
- Single switch
With the modes balance-rr, balance-xor, broadcast and 802.3ad, all physical ports in the link aggregation group must reside on the same logical switch, which, in most common scenarios, will leave a single point of failure when the physical switch to which all links are connected goes offline. The modes active-backup, balance-tlb, and balance-alb can also be set up with two or more switches. But after failover (like all other modes), in some cases, active sessions may fail (due to ARP problems) and have to be restarted.

However, almost all vendors have proprietary extensions that resolve some of this issue: they aggregate multiple physical switches into one logical switch. The Split multi-link trunking (SMLT) protocol allows multiple Ethernet links to be split across multiple switches in a stack, preventing any single point of failure and additionally allowing all switches to be load balanced across multiple aggregation switches from the single access stack. These devices synchronize state across an Inter-Switch Trunk (IST) such that they appear to the connecting (access) device to be a single device (switch block) and prevent any packet duplication. SMLT provides enhanced resiliency with sub-second failover and sub-second recovery for all speed trunks (10 Mbit/s, 100 Mbit/s, 1,000 Mbit/s, and 10 Gbit/s) while operating transparently to end-devices.

Link aggregation is a way of bundling a bunch of individual (Ethernet) links together so they act like a single logical link.

If you have a switch with a whole lot of Gigabit Ethernet ports, you can connect all of them to another device that also has a bunch of ports and balance the traffic among these links to improve performance.

Another important reason for using link aggregation is to provide fast and transparent recovery in case one of the individual links fails.

Individual packets are kept intact and sent from one device to the other over one of the links. In fact, the protocol usually tries to keep whole sessions on a single link. A packet from the next conversation could go over a different link.

The idea is to achieve improved performance by transmitting several packets simultaneously down different links. But standard Ethernet link aggregation never chops up the packet and sends the bits over different links.

The official IEEE standard for link aggregation used to be called 802.3ad, but is now 802.1AX, as I will explain later. However, several vendors have also developed their own proprietary variants.

### Common link aggregation terminology

- A group of ports combined together is called a link aggregation group, or LAG. Different vendors have their own terms for the concept. A LAG can also be called a port-channel, a bond, or a team.
- The rule that defines which packets are sent along which link is called the scheduling algorithm.
- The active monitoring protocol that allows devices to include or remove individual links from the LAG is called Link Aggregation Control Protocol (LACP)

<https://www.auvik.com/franklyit/blog/network-basics-link-aggregation/>
<https://wiki.linuxfoundation.org/networking/bonding>

# Virtual Local Area Networks (VLANs)

- Breaking up one Physical Switch into multiple Virtual Switches
- Extending Virtual Switches across multiple Physical Switches

The switches facilitate communication within networks, and the Routers facilitate communication between networks.
A VLAN allows you to take one physical switch, and break it up into multiple Virtual Switches ( smaller mini-switches). The Virtual Switches operates completely independent from the others. Each virtual switch, or VLAN, is simply a number assigned to each switch port. If a switch port is not explicitly assigned a VLAN number, it resides in the default VLAN, which has a VLAN number of 1.

Traffic arriving on a switch port assigned to VLAN will only ever be forwarded out another switch port that belongs to same VLAN – a switch will never allow traffic to cross a VLAN boundary and each VLAN operates as if it were a completely separate physical switch. The traffic from virtual switch cannot magically appear on the another Virtual switch without first passing through a router.  Each of the VLANs also maintain their own, independent, MAC address table.

Ultimately, assigning different ports to different VLANs allows you to re-use a single physical switch for multiple purposes. This is the first major function of a VLAN.

But that isn’t all VLANs allow you to do. The second major function is VLANs allow you to extend the smaller Virtual switches across multiple Physical switches. The primary benefit of extending a VLAN to different physical switches is that the Layer 2 topology no longer has to be tied to the Physical Topology. A single VLAN can span across multiple rooms, floors, or office buildings.

Each connected switch port in the topologyis a member of only a single VLAN. This is referred to as an **Access port**. An Access port is a switch port that is a member of only one VLAN. When configuring a port as an Access port, the administrator also designates the VLAN number that port is a member of. Whenever the switch receives any traffic on an Access port, it accepts the traffic onto the configured VLAN.

In order to extend a VLAN to the second switch, a connection is made between one Access port on both switches for each VLAN. While functional, this strategy does not scale. Imagine if our topology was using ten VLANs, on a 24 port switch nearly half of the ports would be taken up by the inter-switch links.

Instead, there is a mechanism which allows a single switch port to carry traffic from multiple VLANs. This is referred to as a **Trunk port**. A Trunk port is a switch port that carries traffic for multiple VLANs.

We can use Trunk ports to reduce the amount of switch ports required for the topology above. This enables us to leave more ports available to add hosts to the network in the future.

Typically, switch ports connected to end-host devices are configured as Access ports (e.g., workstations, printers, servers). Conversely, switch ports connected to other network devices are configured as Trunk ports (e.g., other switches, routers).

## Tagged Ports and Untagged Ports

A Trunk port on a switch can receive traffic for more than one VLAN. For example, consider the link between two switches is carrying traffic for both VLAN 10 and VLAN 30.

But in both cases, the traffic is leaving one switch as a series of 1s and 0s, and arriving on the other switch as a series of 1s and 0s. Which begs the question, how will the receiving switch determine which 1s and 0s belong to VLAN #10, and which 1s and 0s belong to VLAN #30?

To account for this, whenever a Switch is forwarding traffic out a Trunk port, it adds to that traffic a tag to indicate to the other end what VLAN that traffic belongs to. This allows the receiving switch to read the VLAN tag in order to determine what VLAN the incoming traffic should be associated to.

An Access port, by comparison, can only ever carry or receive traffic for a single VLAN. Therefore, there is no need to add a VLAN Tag to traffic leaving an Access port.

Since VLANs are a Layer 2 technology, the VLAN Tag is inserted within the Layer 2 header. The standard Layer 2 header in modern networks is the Ethernet header, which has three fields: Destination MAC Address, Source MAC Address, and Type.

When an Ethernet frame is exiting a Trunk port, the switch will insert a VLAN Tag between the Source MAC address and the Type fields.

This allows the receiving switch to associate the frame with the appropriate VLAN.

```
           ______________________________________
          |  Data                | L3    | L2    |
          |______________________|_______|_______|
                       _________________ /       |
                      |__________________________|
                      | Type | SRC MAC | DST MAC |
                      |______|_________|_________|

                __________________________________
               | Type | VLAN | SRC MAC | DST MAC |
               |______|______|_________|_________|

```

## Access Ports and End-Host Devices

Access ports typically face end-host devices like workstations or printers or servers. Part of the reason for this is that switches do not add a VLAN tag when sending traffic out an Access port.

Most end-host devices do not understand the concepts of VLANs. In fact, if they received frames with a VLAN tag inserted in the middle of the Ethernet header, they are likely to drop them under the assumption that they were malformed frames.

Of course, understanding the concepts of VLANs is merely a matter of installing the right software or software patch, but imagine the overhead of requiring every user on your network to both install the software patch, and configure their devices to send the appropriate VLAN tag.

It is much better for the network administrator to configure and concern themselves with VLANs, and for the end-host devices to remain blissfully ignorant of what VLAN they are in, or even whether VLANs are being utilized at all.

## Terminology

What Cisco calls a Trunk port (i.e., a switch port that carries traffic for more than one VLAN), other vendors refer to as a Tagged port – referring to the addition of a VLAN tag to all traffic leaving such a port.

What Cisco calls an Access port (i.e., a switch port that carries traffic for only one VLAN), other vendors refer to as an Untagged port – referring to the traffic leaving the switch port without a VLAN tag.

## 802.1q VLAN Tag

VLAN tags requires adding and removing bits to Ethernet frames. The specific sequence of bits to add is governed by an open standard, which allow any vendor to implement VLANs on their devices.

The exact format of the VLAN Tag is governed by the 802.1q standard. This is an open, IEEE standard which is the ubiquitous method of VLAN tagging in use today

Note: adding and removing the VLAN tag also involves recalculating the CRC — which is a simple hash algorithm devised to detect transmissions errors on the wire.

You can view this capture yourself in Cloudshark, or you can download the capture file and open it in Wireshark.

There is an older method of VLAN tagging which is a closed, Cisco proprietary method. This method was called Inter-Switch Link, or ISL. ISL fully encapsulated the L2 frame in a new header which included the VLAN identification number.

But these days, even newer Cisco products do not support ISL, as the entire industry has moved to the superior, open standard of 802.1q.

## Native VLAN

The Native VLAN is the answer to how a switch processes traffic it receives on a Trunk port which does not contain a VLAN Tag.

Without the tag, the switch will not know what VLAN the traffic belongs to, therefore the switch associates the untagged traffic with what is configured as the Native VLAN. Essentially, the Native VLAN is the VLAN that any received untagged traffic gets assigned to on a Trunk port.

Additionally, any traffic the switch forwards out a Trunk port that is associated with the Native VLAN is forwarded without a VLAN Tag.

The Native VLAN can be configured on any Trunk port. If the Native VLAN is not explicitly designated on a Trunk port, the default configuration of VLAN #1 is used.

That being said, it is crucially important that both sides of a Trunk port are configured with the same Native VLAN else the traffic will not reach the target Host and even worse, due to a Switch’s flooding behavior, other host might inadvertently get the traffic that was destined to target Host C.

Finally, it should be noted that the Native VLAN is an 802.1q feature and Native VLAN concept only applies to Trunk ports — traffic leaving and arriving on an Access port is always expected to be untagged.

<https://www.practicalnetworking.net/stand-alone/vlans/>

# Routing Between VLANs (inter-vlan routing / Router on a Stick (RoaS))

VLANs create a logical separation between Switch ports. Essentially, each VLAN behaves like a separate physical switch. Despite even if hosts are being connected to the same physical switch, the logical topology does not allow hosts in VLAN X to speak with the hosts in VLAN Y. Each VLAN will typically correspond to its own IP Network.

The purpose of a Switch is to facilitate communication within networks and to facilitates communication between networks a Router is required

There are three options available in order to enable routing between the VLANs:

- Router with a Separate Physical Interface in each VLAN
- Router with a Sub-Interface in each VLAN
- Utilizing a Layer 3 Switch

## Router with Separate Physical Interfaces

The simplest way to enable routing between the two VLANs to simply connect an additional port from each VLAN into a Router.

The Router doesn’t know that it has two connections to the same switch — nor does it need to. The Router operates like normal when routing packets between two networks.

The only difference is since there is only one physical switch, there will only be one MAC address table – each entry includes the mapping of switchport to MAC address, as well as the VLAN ID number that port belongs to.

Therefore Each switch port is configured as an Access port. Of course, before assigning the switchport to a VLAN, it is a good idea to create the VLAN in the VLAN Database.

### Router with Sub-Interfaces

The previously described method is functional, but scales poorly. If there were five VLANs on the switch, then we would need five switchports and five router ports to enable routing between all five VLANs. Instead, there exists a way for multiple VLANs to terminate on a single router interface. That method is to create a Sub-Interface.

A Sub-Interface allows a single Physical interface to be split up into multiple virtual sub-interfaces, each of which terminate their own VLAN.

Sub-interfaces to a Router are similar to what Trunk ports are to a Switch – one link carrying traffic for multiple VLANs. Hence, each router Sub-interface must also add a VLAN tag to all traffic leaving said interface.

The logical operation of the Sub-interface topology works exactly as the separate physical interface topology in the section before it. The only difference is with Sub-interfaces, only one Router interface is required to terminate all VLANs.

Keep in mind, however, that the drawback with all VLANs terminating on a single Router interface is an increased risk of congestion on the link.

The Sub-interface feature is sometimes referred to as Router on a Stick or One-armed Router. This is in reference to the single router terminating the traffic from each VLAN.

### Layer 3 Switch

The last option for routing between VLANs does not involve a router at all. Nor does it involve using a traditional switch.

Instead, a different device entirely can be used. This device is known as a Layer 3 Switch (or sometimes also as a Multilayer switch). But exactly what is a Layer 3 switch?

A Layer 3 Switch is different from a traditional Layer 2 Switch in that it has the functionality for routing between VLANs intrinsically. In fact, when considering how a L3 Switch operates, you can safely imagine that a Layer 3 Switch is a traditional switch with a built in Router.

# Promiscuous Mode

In the realm of computer networking, promiscuous mode refers to the special mode of Ethernet hardware, in particular network interface cards (NICs), that allows a NIC to receive all traffic on the network, even if it is not addressed to this NIC. By default, a NIC ignores all traffic that is not addressed to it, which is done by comparing the destination address of the Ethernet packet with the hardware address (a.k.a. MAC) of the device. While this makes perfect sense for networking, non-promiscuous mode makes it difficult to use network monitoring and analysis software for diagnosing connectivity issues or traffic accounting.

In a wider sense, promiscuous mode also refers to network visibility from a single observation point, which doesn't necessarily have to be ensured by putting network adapters in promiscuous mode. Modern hardware and software provide other monitoring methods that lead to the same result



# Layer 3 routing

Layer 3 routing is a crucial network function that determines the optimal path for data packets to travel across a network. It operates at the network layer (Layer 3) of the OSI model, using IP addresses to make routing decisions.
Key Concepts:
 * IP Addresses: Each device on an IP network is assigned a unique IP address, which serves as its identifier.
 * Routing Tables: Routers maintain routing tables that contain information about network destinations and the best paths to reach them.
 * Routing Protocols: These protocols, such as RIP, OSPF, and BGP, help routers exchange routing information and dynamically update their routing tables.


How Layer 3 Routing Works:

 * Packet Reception: When a router receives a data packet, it examines the destination IP address.
 * Routing Table Lookup: The router consults its routing table to find the best route to the destination network. This involves matching the destination IP address with the network prefixes in the routing table.
 * Packet Forwarding: Based on the routing table information, the router forwards the packet to the next hop router or directly to the destination device if it's on the same network.

Benefits of Layer 3 Routing:
 * Network Segmentation: Divides a large network into smaller, more manageable segments.
 * Traffic Optimization: Routes traffic efficiently, reducing latency and improving network performance.
 * Network Scalability: Allows for the addition of new devices and networks without major disruptions.
 * Security: Provides a layer of security by controlling traffic flow between different network segments.
In essence, Layer 3 routing is the backbone of modern IP networks, enabling efficient and reliable communication between devices across diverse and complex network topologies.

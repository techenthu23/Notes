---
title: "Firewall"
draft: false
---

- [Firewall](#firewall)
  - [Firewalld](#firewalld)
  - [Zones](#zones)
    - [Default Pre-Defined Zones](#default-pre-defined-zones)
  - [Firewall Services](#firewall-services)
  - [Masquerading](#masquerading)
  - [STATEFUL VS STATELESS FIREWALLS](#stateful-vs-stateless-firewalls)
    - [DIFFERENCE BETWEEN THE STATEFUL AND STATELESS FIREWALL](#difference-between-the-stateful-and-stateless-firewall)

# Firewall

A firewall generally works at layer 3 and 4 of the OSI model. Layer 3 is the Network Layer where IP works and Layer 4 is the Transport Layer, where TCP and UDP function. Many firewalls today have advanced up the OSI layers and can even understand Layer 7 – the Application Layer.

Firewall types can be divided into several different categories based on their general structure and method of operation. Here are eight types of firewalls:

- Packet-filtering firewalls
- Circuit-level gateways
- Stateful inspection firewalls
- Application-level gateways (a.k.a. proxy firewalls)
- Next-gen firewalls
- Software firewalls
- Hardware firewalls
- Cloud firewalls

## Firewalld

- Dynamic, modern control of system firewall functions
- Still iptables underneath
- based on XML configuration
- FirewallD uses zones and services instead of chain and rules.
- Major features;
  – Real time rule changes without interruption
  – Zones to simplify and segregate configuration
  – Separate network traffic & rules by interface and zone
  – GUI that works
  – System configs in /usr/lib/firewalld/*
  – Custom configs in /etc/firewalld/*
  – Daemon runs in user space
  – Protocol independent: IPv4 & IPv6

Firewalld is the new userland interface in RHEL 7. It replaces the iptables interface and connects to the netfilter kernel code. It mainly improves the security rules management by allowing configuration changes without stopping the current connections.

|                                                        | |
| :----------------------------------------------------- | :--------------------------------------------------------------------------------- |
| firewall-cmd --state                                   | Display whether service is running |
| systemctl status firewalld                             | Another command to display status of service |
| firewall-cmd --reload                                  | To reload the permanent rules without interrupting existing persistent onnections |
| systemctl  < start\|stop\|status > firewalld.service   | To start/stop/status firewalld service |
| systemctl < enable \| disable > firewalld.service      | To enable/disable firewalld service at boot time |
| FIREWALLD_ARGS='--debug' ; systemctl restart firewalld | To better understand how Firewalld works, assign the ‘–debug’ value to the FIREWALLD_ARGS variable in the /etc/sysconfig/firewalld file |

## Zones

- Manages groups of rules
- Dictate what traffic should be allowed
  – Based on level of trust in connected network(s)
  – Based on origin of packet
- A zone can be bound to a network interface and/or to a network addressing (called here a source).
- Zone related configuration files are in /usr/lib/firewalld/zones
- Firewalld provides an initial configuration for each zone in an XML file.

### Default Pre-Defined Zones

| Zone name | Default configuration |
| :-------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| trusted   | Allow all incoming traffic. |
| home      | Reject incoming traffic unless related to outgoing traffic or matching the ssh, mdns, ipp-client, samba-client, or dhcpv6-client predefined services. |
| internal  | Reject incoming traffic unless related to outgoing traffic or matching the ssh, mdns, ipp-client, samba-client, or dhcpv6-client predefined services (same as the home zone to start with). |
| work      | Reject incoming traffic unless related to outgoing traffic or matching the ssh, ipp – client, or dhcpv6 – client predefined services. |
| public    | Reject incoming traffic unless related to outgoing traffic or matching the ssh or dhcpv6 – client predefined services. The default zone for newly added network interfaces |
| external  | Reject incoming traffic unless related to outgoing traffic or matching the ssh predefined service. Outgoing 1Pv4 traffic forwarded through this zone is masqueraded to look like it originated from the 1Pv4 address of the outgoing network interface. |
| dmz       | Reject incoming traffic unless related to outgoing traffic or matching the ssh predefined service. |
| block     | Reject all incoming traffic unless related to outgoing traffic. |
| drop      | Drop all incoming traffic unless related to outgoing traffic (do not even respond with ICMP errors). |

|                                                                                                | |
| :--------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| firewall-cmd ­­--get­-default­-zone                                                                | To display zone which is currently selected as the default |
| firewall-cmd ­­--set-­default-­zone=home                                                           | Tp set the default zone |
| firewall-cmd ­­--get­-zones                                                                       | To get a list of the available zones |
| firewall-cmd ­­--get­-active­-zones                                                                | Get active Zones |
| firewall-cmd ­­--zone=home ­­--list­-all                                                               | To display specific configuration associated with a zone |
| firewall-cmd ­­--list­-all­-zones                                                                  | output all of the zone definitions |
| firewall-cmd ­­--zone=home ­­--change­-interface=eth0                                               | transition  an interface between zones during a session |
| nmcli conn modify <iface> connection.zone <zone>                                               | Modify the zone for the connection in network manager |
| firewall-cmd --zone=public --add-service=samba --add-service=samba-client --permanent          | To add “samba and samba-client” service to a specific zone. You may include, “permanent” flag to make this permanent change. |
| firewall-cmd --zone=public --list-service                                                      | To list services configured in a specific zone |
| firewall-cmd ­­--zone=public ­­--permanent ­­--list-services                                         | To list services permanently configured in a specific zone |
| firewall-cmd --list-ports                                                                      | To list ports |
| firewall-cmd --zone=public --add-port=5000/tcp                                                 | Add port to firewall |
| firewall-cmd ­­zone=public --­­add-­port=4990-­4999/udp                                              | specify a sequential range of ports by separating the beginning and ending port in the range with a dash |
| firewall-cmd --permanent --new-zone=test ; firewall-cmd --reload                               | To create a new zone (here test) |
| firewall-cmd --permanent --zone=trusted --add-source=192.168.2.0/24  ; firewall-cmd --reload   | add a source (here 192.168.2.0/24) to a zone (here trusted) permanently |
| firewall-cmd --permanent --zone=trusted --add-source=00:11:22:33:44:55 ; firewall-cmd --reload | add a source based on a MAC address (here 00:11:22:33:44:55) to a zone (here trusted) permanently: |
| firewall-cmd --permanent --new-ipset=iplist --type=hash:ip ; firewall-cmd --reload             | create an ipset (a set of IP addresses or networks, see below) and add a source based on it: |
| firewall-cmd --ipset=iplist --add-entry=192.168.1.11                                           | |
| firewall-cmd --permanent--zone=trusted --add-source=ipset:iplist ; firewall-cmd --reload       | |
| firewall-cmd --permanent --zone=trusted --list-sources                                         | To get the list of the sources currently bound to a zone (here trusted) |
| firewall-cmd --info-zone=public                                                                | |

## Firewall Services

- Services are sets of firewall rules to open ports associated with a particular application or system service.
- Each service is defined by it's associated .xml file within the /usr/lib/firewalld/services directory. For instance, the SSH service is defined like this: /usr/lib/firewalld/services/ssh.xml
- You can create your own service definitions. The easiest way is to copy & edit an existing service definition. The service you create will takdefinitioine the name of the .xml definition file.

|                                                                                      | |
| :----------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| firewall­cmd ­­zone=public ­­--permanent ­­--add-service=http ; firewall-cmd --reload       | enable a service for a zone |
| firewall-cmd --list-services                                                         | To get the list of services in the default zone |
| firewall-cmd --zone=internal --add-service={http,https,dns}                          | Temporary adds several services (here http, https, and dns) to the internal zone |

## Masquerading

If your firewall is your network gateway and you don’t want everybody to know your internal addresses, you can set up two zones, one called internal, the other external, and configure masquerading on the external zone. This way, all packets will get your firewall ip address as source address.

|                                                                                                    | |
| :------------------------------------------------------------------------------------------------- | :----------------------------------------------------------- |
| firewall­cmd ­­--zone=public --query­-masquerade                                                       | IP Masquerading |
| firewall­cmd ­­--zone=public --add-­masquerade                                                         | enabling masquerading |
| firewall­cmd --zone=public --add-­forward-­port=port=22:proto=tcp:toport=3753 ; firewall-cmd --reload | set up port forwarding to forward packets intended for port 22 to port tcp 3753 |
| firewall-cmd --zone=external --query-forward-port                                                  | Query the configured forward port in the zone |
| firewall­cmd ­­--zone=external --add-­forward-port=port=22:proto=tcp:toaddr=192.0.2.55                 | address forwarding |
| firewall­cmd ­­--zone=external --add­-forward­-port=port=22:proto=tcp:toport=2055:toaddr=192.0.2.55     | both port & address forwarding |
| FIREWALLD_ARGS='--debug' ; systemctl restart firewalld                                             | To better understand how Firewalld works, assign the ‘–debug’ value to the FIREWALLD_ARGS variable in the /etc/sysconfig/firewalld file |


## STATEFUL VS STATELESS FIREWALLS

Stateful firewalls are capable of monitoring all aspects of network traffic, including their communication channels and characteristics. They are also referred to as dynamic pocket filters as they filter traffic packets based on the context and state.

- Context – it involves metadata of packets including ports and IP address belonging to the endpoint’s and destination, packet length, layer 3 information related to reassembly and fragmentation, flags, and numbers for TCP sequence of layer 4, and more.
- State – firewalls apply their policy based on the state of the connection. To understand the state, let’s take the example of TCP-based communication. In TCP, 4 bits control connection state – SYN, ACK, FIN, and RST.
  When a connection initiates through a 3-way handshake, then the TCP indicates the SYN flag, which the firewall uses to indicate the arrival of a new connection. Next, the connection receives the flag SYN+ACK by the server. Until the client reverts with ACK, the connection does not establish. Similarly, on seeing FIN+ACK or RST packet, the connection is marked for deletion right there along with for future packets.

Stateless firewalls monitor the incoming traffic packets. They allow or deny packets into their network based on the source and the destination address, or some other information like traffic type. They just monitor some basic information of the packets and restriction or permission depends upon that.

For example, the rule below accepts all TCP packets from the 192.168.1.x subnet that are bound for port 80.

```
-A INPUT -p tcp -s 192.168.1.0/24 -m tcp --dport 80 -j ACCEPT
```

For output, the rules look similar. Therefore, outgoing(egress) packets are only accepted if there is a matching rule. The rule below allows only outgoing packets on port 80.

```
-A OUTPUT -o eth0 -p tcp --sport 80 -j ACCEPT
```

For a stateful firewall, you have the ability to monitor state. For example, the rule below only accepts packets to port 80 if it is initiating a new connection or is associated with an existing connection. The tracking is done by a kernel module "ip_conntrack". It keeps a table of all active connections.

```
-A INPUT -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
```

Refer 
- https://wiki.archlinux.org/title/simple_stateful_firewall
- https://firewalld.org/2020/09/policy-objects-introduction

  
### DIFFERENCE BETWEEN THE STATEFUL AND STATELESS FIREWALL

Stateful firewalls are smarter and responsible to monitor and detect the end-to-end traffic stream, and to defend according to the traffic pattern and flow. It filters the packets based on the full context given to the network connection.   These firewalls are faster and work excellently, under heavy traffic flow. They are also better at identifying forged or unauthorized communication.

On the other hand, a stateless firewall is basically an Access Control List ( ACLs) that contains the set of rules which allows or restricts the flow of traffic depending upon the source, IP address, destination, port number, network protocols, and some other related fields. This firewall doesn’t interfere in the traffic flow, they just go through the basic information about them, and allowing or discard depends upon that. But there is a chance for the forged packets or attack techniques may fool these firewalls and may bypass them.

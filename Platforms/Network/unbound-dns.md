---
title: Unbound DNS
fraft: false
---

- [Introduction](#introduction)
  - [Domains and name delegation](#domains-and-name-delegation)
- [So why Unbound?](#so-why-unbound)
  - [BIND](#bind)
  - [DNSMasq](#dnsmasq)
  - [Unbound](#unbound)
    - [Why Run Your Own DNS Resolver](#why-run-your-own-dns-resolver)
    - [Unbound DNS Resolver](#unbound-dns-resolver)
    - [Installation](#installation)
- [HOWTO setup your own caching nameserver using Unbound](#howto-setup-your-own-caching-nameserver-using-unbound)
  - [Basic installation and configuration](#basic-installation-and-configuration)
    - [Validating your queries](#validating-your-queries)
      - [Maintaining a root "hints" file](#maintaining-a-root-hints-file)
  - [Forwarding queries](#forwarding-queries)
  - [Ensuring there is no systemd or networkmanager interference](#ensuring-there-is-no-systemd-or-networkmanager-interference)
    - [Unbound not serving PTR records for private address space](#unbound-not-serving-ptr-records-for-private-address-space)
    - [unbound.service takes around 50 seconds to start](#unboundservice-takes-around-50-seconds-to-start)
  - [Why does /etc/resolv.conf point at 127.0.0.53?](#why-does-etcresolvconf-point-at-1270053)
- [Build from scratch](#build-from-scratch)

# Introduction

As sysadmins, we need to know a bit about what [DNS](https://www.redhat.com/sysadmin/dns-domain-name-servers) is and how it works — including what could go wrong. Knowing all of that, what advantage would there be in running our very own DNS server at home or in our small organization? There could be several reasons you might want to have your own DNS server.

- A local DNS server can decrease response time for address queries, and make more efficient use of network resources, improving performance overall.
- A local DNS server can be used to filter queries. For example, it may block DNS resolution of sites serving advertising or malware.
- Some people run their own DNS server out of concerns for privacy and the security of data.
- You might want your own DNS server in your own home lab or small organization to manage internal, local name resolution. In addition, you do not have to remember addresses, rely on an external DNS service, or maintain hosts files on all your devices.

## Domains and name delegation

The domain namespace is a tree structure. The entire database can be seen as an inverted tree, with the root at the top. Each node and leaf on the tree corresponds to a resource set. The name of the root is the empty string (" ") generally denoted with a dot (.):

0) `.` root
1) `.net`, `.com`, `.gov` are Top Level Dcomains
2) `example.com` is Second Level Domain
3) `www.example.com`, `cluster.example.com`, `mail.example.com` are Subdomains
4) `node1.cluster.example.com` are hosts

A domain name identifies a set of resources that, in turn, is integrated with the separate resource registry (RR). One of the main components of the RRs is the identification of the resource that stores this record, and hence is the owner or the authoritative source of the information.

Public domain names are registered with a registrar authorized by [ICANN](https://www.icann.org/), a non-profit entity that regulates them. In most cases, these registrants offer the service of taking over the domain name's administration. This process is known as domain name delegation.

The most common types of RRs to verify are:

- A - The host address associated with the domain name.
- NS - The authoritative name server for the domain.
- SOA - Identifies the start of a zone of authority.
- CNAME - Identifies the canonical name of an alias.
- PTR - Identifies the IP address associated with the domain/hostname.

For example, if we want to validate the record type A of the redhat.com domain:

```
dig redhat.com
```

The PTR (or reverse) record query is used to validate that the IP address is assigned to the same host that is resolved in the Mail eXchanger (MX) record query:

```
dig redhat.com -t MX
dig -x 209.132.183.28**
```

This configuration is necessary when the domain manages its own mail service because otherwise, the server can be blacklisted and untrusted.

Another useful troubleshooting procedure is to review the trace left by the routing of our query:

```
dig redhat.com @localhost +trace
```

In this query, you can see that it starts in the root DNS servers, who walk to the Top-Level Domains (TLDs) until the domain name registration requested is found and returns the hosts that are identified with it.

A domain name is assigned to several authoritative hosts. This is a setting similar to the cluster, in order to provide availability in the resolution service.

# So why Unbound?

There are many options to choose from for this project. For the sake of discussion, we'll talk briefly about a popular example of the three main types (note that we'll only consider 'open' software that you can get without having to pay for a license).

## BIND

[BIND](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-bind) is the grandfather of DNS servers, the first and still the most common of the available options. BIND comes capable of anything you would want to do with a DNS server — notably, it provides an authoritative DNS server. It can manage many (like hundreds of) zones or domains as the final word on addressing. All these features make it slightly harder to configure and manage than some other options, and it's slower than the others as well. It can quickly become complicated to manage and is probably overkill for a smaller project.

## DNSMasq

[DNSMasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) is a lightweight caching server designed for performance and ease of implementation. It is also packaged with a simple DHCP and TFTP server. It's very popular as part of software packaged for home use and is an underlying piece of some other software you might have used like Clonezilla and Pi-Hole because it can provide all these services as a single small package. Unfortunately, even though it's capable of split-DNS, it is a caching-only server. It can't do recursion (it can't look for another DNS server or handle referrals to or from other servers), and it can't host even a stub domain, so it's not too helpful managing names and addresses.

## Unbound

[Unbound](https://nlnetlabs.nl/projects/unbound/about/) can be a caching server, but it can also do recursion and keep records it gets from other DNS servers as well as provide *some* authoritative service, like if you have just a few zones — so it can serve as a stub or "glue" server, or host a small zone of just a few domains — which makes it perfect for a lab or small organization. It's also very popular as a recursive and caching layer server in larger deployments. Unbound is capable of DNSSEC validation and can serve as a trust anchor. It can do TLS encryption, and the most recent version now implements the RPZ standard (a more robust and sophisticated version of what DNSMasq does with split-DNS to allow the filtering of DNS queries for privacy and security). It's also become the standard default DNS server software available for many GNU/Linux distributions, including BSD and Red Hat-based versions.

In my own lab, I'm running a BIND authoritative server for an internal domain, and I want to add an Unbound server that refers to this but can also cache, recurse, and forward requests to the outside world. The only reason I'm doing these separately is for reference and practice.

### Why Run Your Own DNS Resolver

Usually, your computer, router or server uses your ISP’s DNS resolver to query DNS names, so why run a local DNS resolver?

- It can speed up DNS lookups, because the local DNS resolver only listens to your DNS requests and does not answer other people’s DNS requests, so you have a much higher chance of getting DNS answers directly from the cache on the resolver. The network latency between your computer and DNS resolver is eliminated (almost zero), so DNS queries can be sent to root DNS servers more quickly.
- If you run a mail server and use DNS blacklists (DNSBL) to block spam, then you should run your own DNS resolver, because some DNS blacklists such as URIBL refuse requests from public DNS resolvers.
- If you run your own VPN server on a VPS (Virtual Private Server), it’s also a good practice to install a DNS resolver on the same VPS.
- You may also want to run your own DNS resolver if you don’t like your Internet browsing history being stored on a third-party server.

Hint: Local doesn’t mean your home computer. Rather, it means the DNS resolver runs on the same box or the same network as the DNS client. You can install Unbound DNS resolver on your home computer. It’s local to your home computer. You can also install Unbound DNS resolver on a cloud server, and it’s local to the cloud server.

### Unbound DNS Resolver

Unbound is an open-source DNS validating resolver, meaning it can do DNSSEC validation to ensure the DNS response is authentic. Unbound features:

- Lightweight and extremely fast, as it doesn’t provide full-blown authoritative DNS server functionality. On one of my servers, Unbound uses a quarter of the memory required by BIND9.
- DNS response cache
- Prefetch: fetch data that is about to expire so that client does not get spike in latency when lookup needs to be redone when TTL expires on data.
- DNS over TLS
- DNS over HTTPS
- Query Name Minimization: Send minimum amount of information to upstream servers to enhance privacy.
- Aggressive Use of DNSSEC-Validated Cache
- Authority zones, for a local copy of the root zone
- DNS64
- DNSCrypt
- DNSSEC validation: It’s enabled by default on Ubuntu
- EDNS Client Subnet
- Can run as a DNS forwarder.
- Supports local-data and response policy zone to give a custom answer back for certain domain names.

### Installation

From RHEL/CENTOS/Fedora machines, it's as simple as getting it from the main YUM repositories:

```
[root@callisto ~]# yum install unbound
 
---> Package unbound.x86_64 0:1.6.6-1.el7 will be installed
---> Package libevent.x86_64 0:2.0.21-4.el7 will be installed
---> Package unbound-libs.x86_64 0:1.6.6-1.el7 will be installed
--> Finished Dependency Resolution

Total download size: 1.3 M
Installed size: 4.2 M
```

The main file we'll be working with to configure unbound is the `unbound.conf` file, which on RHEL/CentOS/Fedora is at `/etc/unbound/unbound.conf`

For this project, I'm going to install Unbound as a caching/recursive DNS server with the additional job of resolving machines in my local lab via an already existing DNS server that acts as an authoritative server for my lab and home office.

Basic configuration

First find and uncomment these two entries in `unbound.conf`:

```
interface: 0.0.0.0
interface: ::0
```

Here, the `0` entry indicates that we'll be accepting DNS queries on all interfaces. If you have more than one interface in your server and need to manage where DNS is available, you would put the address of the interface here.

Next, we may want to control who is allowed to use our DNS server. We're going to limit access to the local subnets we're using. It's a good basic practice to be specific when we can:

```
Access-control: 127.0.0.0/8 allow  # (allow queries from the local host)
access-control: 192.168.0.0/24 allow
access-control: 192.168.1.0/24 allow
```

We also want to add an exception for local, unsecured domains that aren't using DNSSEC validation:

```
domain-insecure: "forest.local"
```

Now I’m going to add my local authoritative BIND server as a stub-zone:

```
stub-zone:
        name: "forest"
        stub-addr: 192.168.0.220
        stub-first: yes
```

If you want or need to use your Unbound server as an authoritative server, you can add a set of local-zone entries that look like this:

```
local-zone:  “forest.local.” static

local-data: “jupiter.forest”         IN       A        192.168.0.200
local-data: “callisto.forest”        IN       A        192.168.0.222
```

These can be any type of record you need locally but note again that since these are all in the main configuration file, you might want to configure them as stub zones if you need authoritative records for more than a few hosts (see above).

If you were going to use this Unbound server as an authoritative DNS server, you would also want to make sure you have a `root hints` file, which is the zone file for the root DNS servers.

Get the file from [InterNIC](https://www.internic.net/). It is easiest to download it directly where you want it. My preference is usually to go ahead and put it where the other unbound related files are in `/etc/unbound`:

```
wget https://www.internic.net/domain/named.root -O /etc/unbound/root.hints
```

Then add an entry to your `unbound.conf` file to let Unbound know where the hints file goes:

```
# file to read root hints from.
        root-hints: "/etc/unbound/root.hints"
```

Finally, we want to add at least one entry that tells Unbound where to forward requests to for recursion. Note that we could forward specific domains to specific DNS servers. In this example, I'm just going to forward everything out to a couple of DNS servers on the Internet:

```
forward-zone:
        name: "."
        forward-addr: 1.1.1.1
        forward-addr: 8.8.8.8
```

Now, as a sanity check, we want to run the `unbound-checkconf` command, which checks the syntax of our configuration file. We then resolve any errors we find.

```
[root@callisto ~]# unbound-checkconf
/etc/unbound/unbound_server.key: No such file or directory
[1584658345] unbound-checkconf[7553:0] fatal error: server-key-file: "/etc/unbound/unbound_server.key" does not exist
```

This error indicates that a key file which is generated at startup does not exist yet, so let's start Unbound and see what happens:

```
[root@callisto ~]# systemctl start unbound
```

With no fatal errors found, we can go ahead and make it start by default at server startup:

```
[root@callisto ~]# systemctl enable unbound
Created symlink from /etc/systemd/system/multi-user.target.wants/unbound.service to /usr/lib/systemd/system/unbound.service.
```

And you should be all set. Next, let's apply some of our DNS troubleshooting skills to see if it's working correctly.

First, we need to set our DNS resolver to use the new server:

```
[root@showme1 ~]# nmcli con mod ext ipv4.dns 192.168.0.222
[root@showme1 ~]# systemctl restart NetworkManager
[root@showme1 ~]# cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 192.168.0.222
[root@showme1 ~]#
```

Let's run `dig` and see who we can see:

```
root@showme1 ~]# dig
; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>>
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 36486
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;.                              IN      NS

;; ANSWER SECTION:
.                       508693  IN      NS      i.root-servers.net.
<snip>
```

Excellent! We are getting a response from the new server, and it's recursing us to the root domains.  We don't see any errors so far.  Now to check on a local host:

```
;; ANSWER SECTION:
jupiter.forest.         5190    IN      A       192.168.0.200
```

Great! We are getting the A record from the authoritative server back, and the IP address is correct. What about external domains?

```
;; ANSWER SECTION:
redhat.com.             3600    IN      A       209.132.183.105
```

Perfect! If we rerun it, will we get it from the cache?

```
;; ANSWER SECTION:
redhat.com.             3531    IN      A       209.132.183.105

;; Query time: 0 msec
;; SERVER: 192.168.0.222#53(192.168.0.222)
```

Note the Query time of `0` seconds- this indicates that the answer lives on the caching server, so it wasn't necessary to go ask elsewhere. This is the main benefit of a local caching server, as we discussed earlier.

Wrapping up

While we did not discuss some of the more advanced features that are available in Unbound, one thing that deserves mention is DNSSEC. DNSSEC is becoming a standard for DNS servers, as it provides an additional layer of protection for DNS transactions. DNSSEC establishes a trust relationship that helps prevent things like spoofing and injection attacks. It's worth looking into a bit if you are using a DNS server that faces the public even though It's beyond the scope of this article.

# HOWTO setup your own caching nameserver using Unbound

The best alternative for setting up a caching nameserver on your LAN or personal machine today is Unbound. It's written with that purpose in mind which makes it faster and simpler to setup than more full-featured alternatives such as _**named**_ (called "BIND") or KNOT-resolver and such.

## Basic installation and configuration

Start off by installing unbound with `dnf install unbound` or `apt-get install unbound` depending on distribution. On Arch you also have to manually install the `expat` package.

Now it's time to make a configuration file for it. Here is a most basic example of what you need: **File:**  /etc/unbound/unbound.confserver:

```
  access-control: 10.0.0.0/8 allow
  access-control: 192.168.0.0/16 allow
  access-control: 127.0.0.0/8 allow
  access-control: ::1/128 allow
  interface: ::
  interface: 0.0.0.0
  aggressive-nsec: yes
  cache-max-ttl: 14400
  cache-min-ttl: 300
  hide-identity: yes
  hide-version: yes
  minimal-responses: yes
  prefetch: yes
  qname-minimisation: yes
  rrset-roundrobin: yes
  use-caps-for-id: yes
  verbosity: 0
```

The first few lines define who can query the server with `access-control` directives. They we define what to listen on with `interface`. The above example is very liberal and makes unbound listen on all interfaces.

Refer to the [unbound.conf manual page](https://man.linuxreviews.org/man5/unbound.conf.5.html) for further details.

It is a good idea to run `unbound-checkconf` to check if it is valid once you are happy with your configuration.

Now you need to ensure that `systemd-resolved` is not occupying the DNS port. You can do this by giving it the following configuration file: **File:**  /etc/systemd/resolved.conf

```
[Resolve] 
DNS=127.0.0.1 
FallbackDNS=1.0.0.1 
MulticastDNS=no 
DNSStubListener=no
```

The `DNSStubListener` directive is essential to ensure it does not listen for DNS queries. You may actually want `MulticastDNS` if you do not use `avahi-daemon` for multicast-DNS purposes.

Restart systemd-resolved with `systemctl restart systemd-resolved.service` or stop it with code\>systemctl stop systemd-resolved.service - *Do be aware that it doesn't really matter if you stop it or re-start it. systemd will happily start it when applications make API requests for it even if you masked it.*

Then run

`systemctl start unbound.service`

to start it and

`systemctl enable unbound.service`

to make it start on every boot.

### Validating your queries

Once you have a basic unbound setup you will want to add  **validation**. First, install the `bind-utils` package and copy the `/etc/trusted-key.key` to where unbound can read it with `cp /etc/trusted-key.key /etc/unbound`

Now add this line to your unbound.conf:

`trust-anchor-file: "/etc/unbound/trusted-key.key"`

And restart unbound. Now you should test using this command:

`unbound-host -C /etc/unbound/unbound.conf -v sigok.verteiltesysteme.net`

which should output

```
sigok.verteiltesysteme.net has address 134.91.78.139 (secure)
sigok.verteiltesysteme.net has IPv6 address 2001:638:501:8efc::139 (secure)
sigok.verteiltesysteme.net has no mail handler record (secure)
```

and NOT

```
sigok.verteiltesysteme.net has address 134.91.78.139 (insecure)
sigok.verteiltesysteme.net has IPv6 address 2001:638:501:8efc::139 (insecure)
sigok.verteiltesysteme.net has no mail handler record (insecure)
```

If get "secure" as in the first output then you're good, if you get "insecure" as in the last example then it is not working.

#### Maintaining a root "hints" file

A DNS query will first go to the DNS root and then the nameservers responsible for the top domain (`.com/.org/etc`) and then the server which is responsible for the domain you are querying. Thus; you need to know where to start. Unbound comes with a built-in list of root servers. That's great, but if your Unbound version gets wildly outdated and the root servers change those hints may no longer apply. It is considered "good practice" to keep a updated local copy of the hints. A simple way to do this is to create a good old `cron` file: **File:**  /etc/cron.monthly/update-unbound-hints.sh

```bash
#!/bin/bash 
wget -q https://www.internic.net/domain/named.root -O /tmp/root.hints 
if grep -q ROOT-SERVERS /tmp/root.hints ;then 
  mv -f /tmp/root.hints /etc/unbound/root.hints
  chmod a+r /etc/unbound/root.hints 
fi
```

make the file executable `chmod a+x /etc/cron.monthly/update-unbound-hints.sh`

and run it `/etc/cron.monthly/update-unbound-hints.sh`

Then add the following line to `/etc/unbound/unbound.conf` :

```
root-hints: "/etc/unbound/root.hints
```

## Forwarding queries

Asking a botnet DNS server ran by either CloudFlare or Google may actually be faster than doing your own recursive lookups depending on your network, location and distance to those.

If you would like to forward DNS queries to another nameserver on your LAN you need to add

```
forward-zone:
   name: "."
   forward-addr: 192.168.0.1@53
```

That is good enough on your LAN but you may want to add more if your DNS queries will be leaving your local network and going to the botnet. For this you would want to add the following:

```
  tls-upstream: yes 
  tls-cert-bundle: /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem 
forward-zone: 
  name: "." 
  # Cloudflare DNS 
  forward-addr: 2606:4700:4700::1111@853#cloudflare-dns.com 
  forward-addr: 1.1.1.1@853#cloudflare-dns.com 
  forward-addr: 2606:4700:4700::1001@853#cloudflare-dns.com 
  forward-addr: 1.0.0.1@853#cloudflare-dns.com
```

Here we tell unbound to use TLS for queries leaving the LAN with `tls-upstream: yes`. Then we tell it what certificate package to check against with `tls-cert-bundle`. The file referred to should be installed by the `ca-certificates` package which you should have gotten installed as a dependency for unbound. Next we specify a set of `forward-addr` and  **here there is one detail one might get wrong:**  The *names* of those servers after the `#` are *not* comments. Those are used for the TLS certificate validation and *must be present*.

Combining the above a complete unbound configuration file which forwards DNS queries to the CloudFlare botnet looks like this: **File:**  /etc/unbound/unbound.conf

```
server:
  access-control: 10.0.0.0/8 allow
  access-control: 192.168.0.0/16 allow
  access-control: 127.0.0.0/8 allow
  access-control: ::1/128 allow
  interface: ::
  interface: 0.0.0.0
  aggressive-nsec: yes
  cache-max-ttl: 14400
  cache-min-ttl: 300
  hide-identity: yes
  hide-version: yes
  minimal-responses: yes
  prefetch: yes
  qname-minimisation: yes
  rrset-roundrobin: yes
  use-caps-for-id: yes
  verbosity: 0
  tls-upstream: yes 
  tls-cert-bundle: /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem 
forward-zone:
  name: "." 
  # Cloudflare DNS 
  forward-addr: 2606:4700:4700::1111@853#cloudflare-dns.com 
  forward-addr: 1.1.1.1@853#cloudflare-dns.com 
  forward-addr: 2606:4700:4700::1001@853#cloudflare-dns.com 
  forward-addr: 1.0.0.1@853#cloudflare-dns.com
```

## Ensuring there is no systemd or networkmanager interference

**This section is not for everyone.**  You may want to ensure that a local or LAN Unbound nameserver is always used regardless of systemd or networkmanager opinion. To accomplish this you first need to tell networkmanager to not interfere with your DNS settings, ever. This can be accomplished by changing the configuration file `/etc/NetworkManager/NetworkManager.conf` to only contain the following content: **File:**  /etc/NetworkManager/NetworkManager.conf

```
[main]
dns=none
systemd-resolved=false
[logging]
```

Now NetworkManager will not touch or overwrite or destroy your `/etc/resolv.conf`. By default that file is now a symbolic link on most distributions. You can remove that link and demand that only localhost, where you now have unbound, is (ab)used with these fine commands:

```
rm -f /etc/resolv.conf
echo 'nameserver 127.0.0.1' > /etc/resolv.conf
```

Note that you are now relying entirely on unbound running on localhost. That is fine if you are starting Unbound on boot and you want it to handle DNS regardless of what network you are using.

[systemd-resolved](https://linuxreviews.org/Systemd-resolved) will however be a pain to configure in this situation.

### Unbound not serving PTR records for private address space

**Environment**

    Red Hat Enterprise Linux 7

**Issue**

We have unbound configured as a caching-only name server:

```
# grep forward-addr /etc/unbound/unbound.conf 
    forward-addr: 10.10.1.2@53
    forward-addr: 10.10.3.2@53
```

When we do a reverse-lookup for an IP address (a PTR record lookup) using the local unbound service, NXDOMAIN is returned:

```
# host 192.168.1.3 127.0.0.1
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases: 

Host 3.1.168.192.in-addr.arpa. not found: 3(NXDOMAIN)
```

However querying the forwarded name server works correctly:

```
# host 192.168.1.5 10.10.1.2
Using domain server:
Name: 10.10.1.2
Address: 10.10.1.2#53
Aliases: 

5.1.168.192.in-addr.arpa domain name pointer host.example.com.
```

**Resolution**
To perform reverse look-ups on IP addresses in the RFC1918 private address space, the appropriate local-zone directive listed in /etc/unbound/unbound.conf should be un-commented. The local-zone directives that are commented by default are:

```
    # By default, for a number of zones a small default 'nothing here'
    # reply is built-in.  Query traffic is thus blocked.  If you
    # wish to serve such zone you can unblock them by uncommenting one
    # of the nodefault statements below.
    # You may also have to use domain-insecure: zone to make DNSSEC work,
    # unless you have your own trust anchors for this zone.
    # local-zone: "localhost." nodefault
    # local-zone: "127.in-addr.arpa." nodefault
    # local-zone: "1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa." nodefault
    # local-zone: "onion." nodefault
    # local-zone: "test." nodefault
    # local-zone: "invalid." nodefault
    # local-zone: "10.in-addr.arpa." nodefault
    # local-zone: "16.172.in-addr.arpa." nodefault
    # local-zone: "17.172.in-addr.arpa." nodefault
    # local-zone: "18.172.in-addr.arpa." nodefault
    # local-zone: "19.172.in-addr.arpa." nodefault
    # local-zone: "20.172.in-addr.arpa." nodefault
    # local-zone: "21.172.in-addr.arpa." nodefault
    # local-zone: "22.172.in-addr.arpa." nodefault
    # local-zone: "23.172.in-addr.arpa." nodefault
    # local-zone: "24.172.in-addr.arpa." nodefault
    # local-zone: "25.172.in-addr.arpa." nodefault
    # local-zone: "26.172.in-addr.arpa." nodefault
    # local-zone: "27.172.in-addr.arpa." nodefault
    # local-zone: "28.172.in-addr.arpa." nodefault
    # local-zone: "29.172.in-addr.arpa." nodefault
    # local-zone: "30.172.in-addr.arpa." nodefault
    # local-zone: "31.172.in-addr.arpa." nodefault
    # local-zone: "168.192.in-addr.arpa." nodefault
    # local-zone: "0.in-addr.arpa." nodefault
    # local-zone: "254.169.in-addr.arpa." nodefault
    # local-zone: "2.0.192.in-addr.arpa." nodefault
    # local-zone: "100.51.198.in-addr.arpa." nodefault
    # local-zone: "113.0.203.in-addr.arpa." nodefault
    # local-zone: "255.255.255.255.in-addr.arpa." nodefault
    # local-zone: "0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa." nodefault
    # local-zone: "d.f.ip6.arpa." nodefault
    # local-zone: "8.e.f.ip6.arpa." nodefault
    # local-zone: "9.e.f.ip6.arpa." nodefault
    # local-zone: "a.e.f.ip6.arpa." nodefault
    # local-zone: "b.e.f.ip6.arpa." nodefault
    # local-zone: "8.b.d.0.1.0.0.2.ip6.arpa." nodefault
```

A `domain-insecure` directive should also be added to unbound.conf for every local-zone that is uncommented.

For example, un-commenting:

```
local-zone: "168.192.in-addr.arpa." nodefault
```

and adding:

```
domain-insecure: "168.192.in-addr.arpa"
```

to unbound.confresults in being able to look up the PTR record to get host names for IP addresses in the private 192.168.0.0/16 subnet. For example:

```
# dig -x 192.168.9.2

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>> -x 192.168.9.2
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 49249
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;2.9.168.192.in-addr.arpa.  IN  PTR

;; ANSWER SECTION:
2.9.168.192.in-addr.arpa. 1800 IN   PTR rht590.example.com

;; Query time: 182 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Tue Apr 28 14:56:48 PDT 2020
;; MSG SIZE  rcvd: 92
```

**Root Cause**

As explained in /etc/unbound/unbound.conf:

```
# By default, for a number of zones a small default 'nothing here'
# reply is built-in.  Query traffic is thus blocked.        
# You may also have to use domain-insecure: zone to make DNSSEC work,
# unless you have your own trust anchors for this zone.
```

### unbound.service takes around 50 seconds to start

**Environment**

    Red Hat Enterprise Linux 7
    unbound

**Issue**

    Configuring unbound and time delay in starting up
    We require to use unbound as a local dns cache querying only our DNS forwarders and the service is quite slow, it takes around 50 seconds to start.

**Resolution**

    Create an alternative resolv.conf file with the forwarder nameservers and modify unbound.service unit and ExectStartPre= option to make unbound-anchor use the forwarders instead of the root nameservers to update the root trust:

```
# echo "nameserver x.x.x.x" >> /etc/resolv-forwarders.conf
# echo "nameserver y.y.y.y" >> /etc/resolv-forwarders.conf

# systemctl edit --full unbound.service 

ExecStartPre=-/usr/sbin/unbound-anchor -a /var/lib/unbound/root.key -c /etc/unbound/icannbundle.pem -f /etc/resolv-forwarders.conf

# systemctl restart unbound.service
```

**Root Cause**

    When using unbound to query only forwarder nameservers and all remaining DNS traffic is restricted by a firewall, unbound.service fails to contact the root nameservers with process unbound-anchor (ExecStartPre= in systemd unit) as this process tries to update the root trust until it gives up.

**Diagnostic Steps**

    Checking unbound.service status with systemctl almost a minute delay can be seen between unbound-checkconf and unbound processes log records

```
[root@server ~]# systemctl status -l unbound.service                                                                                 
● unbound.service - Unbound recursive Domain Name Server
   Loaded: loaded (/usr/lib/systemd/system/unbound.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-01-27 07:36:22 EST; 32s ago
  Process: 23356 ExecStartPre=/usr/sbin/unbound-anchor -a /var/lib/unbound/root.key -c /etc/unbound/icannbundle.pem (code=exited, status=0/SUCCESS)
  Process: 23355 ExecStartPre=/usr/sbin/unbound-checkconf (code=exited, status=0/SUCCESS)
 Main PID: 31054 (unbound)
   CGroup: /system.slice/unbound.service
           └─31054 /usr/sbin/unbound -d

Jan 27 07:35:31 server systemd[1]: Starting Unbound recursive Domain Name Server...
Jan 27 07:35:31 server unbound-checkconf[23355]: unbound-checkconf: no errors in /etc/unbound/unbound.conf
Jan 27 07:36:22 server systemd[1]: Started Unbound recursive Domain Name Server.
Jan 27 07:36:22 server unbound[31054]: Jan 27 07:36:22 unbound[31054:0] notice: init module 0: iterator
Jan 27 07:36:22 server unbound[31054]: Jan 27 07:36:22 unbound[31054:0] info: start of service (unbound 1.6.6).
```

## Why does /etc/resolv.conf point at 127.0.0.53?

You are likely running systemd-resolved as a service.

systemd-resolved generates two configuration files on the fly, for optional use by DNS client libraries (such as the BIND DNS client library in C libraries):

- `/run/systemd/resolve/stub-resolv.conf` tells DNS client libraries to send their queries to 127.0.0.53. This is where the systemd-resolved process listens for DNS queries, which it then forwards on.
- `/run/systemd/resolve/resolv.conf` tells DNS client libraries to send their queries to IP addresses that systemd-resolved has obtained on the fly from its configuration files and DNS server information contained in DHCP leases. Effectively, this bypasses the systemd-resolved forwarding step, at the expense of also bypassing all of systemd-resolved's logic for making complex decisions about what to actually forward to, for any given transaction.

-
In both cases, systemd-resolved configures a search list of domain name suffixes, again derived on the fly from its configuration files and DHCP leases (which it is told about via a mechanism that is beyond the scope of this answer).

`/etc/resolv.conf` can optionally be:

- a symbolic link to either of these;
- a symbolic link to a package-supplied static file at `/usr/lib/systemd/resolv.conf`, which also specifies `127.0.0.53` but no search domains calculated on the fly;
- some other file entirely.

It's likely that you have such a symbolic link. In which case, the thing that knows about the `192.168.1.1` setting, that is (presumably) handed out in DHCP leases by the DHCP server on your LAN, is systemd-resolved, which is forwarding query traffic to it as you have observed. Your DNS client libraries, in your applications programs, are themselves only talking to systemd-resolved.

Ironically, although it could be that you haven't captured loopback interface traffic to/from `127.0.0.53` properly, it is more likely that you aren't seeing it because systemd-resolved also (optionally) bypasses the BIND DNS Client in your C libraries and generates no such traffic to be captured.

There's an NSS module provided with systemd-resolved, named nss-resolve, that is a plug-in for your C libraries. Previously, your C libraries would have used another plug-in named nss-dns which uses the BIND DNS Client to make queries using the DNS protocol to the server(s) listed in `/etc/resolv.conf`, applying the domain suffixes listed therein.

nss-resolve gets listed ahead of nss-dns in your `/etc/nsswitch.conf` file, causing your C libraries to not use the BIND DNS Client, or the DNS protocol, to perform name→address lookups at all. Instead, nss-resolve speaks a non-standard and idiosyncratic protocol over the (system-wide) Desktop Bus to systemd-resolved, which again makes back end queries of `192.168.1.1` or whatever your DHCP leases and configuration files say.

To intercept that you have to monitor the Desktop Bus traffic with dbus-monitor or some such tool. It's not even IP traffic, let alone IP traffic over a loopback network interface. as the Desktop Bus is reached via an AF_LOCAL socket.

If you want to use a third-party resolving proxy DNS server at 1.1.1.1, or some other IP address, you have three choices:

    Configure your DHCP server to hand that out instead of handing out 192.168.1.1. systemd-resolved will learn of that via the DHCP leases and use it.
    Configure `systemd-resolved` via its own configuration mechanisms to use that instead of what it is seeing in the DHCP leases.
    Make your own `/etc/resolv.conf` file, an actual regular file instead of a symbolic link, list `1.1.1.1` there and remember to turn off nss-resolve so that you go back to using nss-dns and the BIND DNS Client.

The systemd-resolved configuration files are a whole bunch of files in various directories that get combined, and how to configure them for the second choice aforementioned is beyond the scope of this answer. Read the resolved.conf(5) manual page for that.



# Build from scratch

Build the latest version
```
ver=1.13.1
wget https://nlnetlabs.nl/downloads/unbound/unbound-$ver.tar.gz
sha1sum unbound-$ver.tar.gz
# 9932931d495248b4e45d278b4679efae29238772  unbound-1.10.1.tar.gz
# 561522b06943f6d1c33bd78132db1f7020fc4fd1  unbound-1.13.1.tar.gz
tar xzf unbound-$ver.tar.gz
cd unbound-$ver/
./configure --help | less

# slackware
./configure --with-libevent --disable-systemd

# ubuntu
./configure --with-libevent

echo $MAKEFLAGS
make > ../unbound.log && echo BUILT
make install
ldconfig
```

Create a system user for Unbound to drop its priviledges
```
groupadd -g 499 unbound
useradd --system -M -d /var/chroot/unbound -s /sbin/nologin -g unbound -u 499 unbound

grep unbound /etc/passwd
grep unbound /etc/group

mkdir -p /var/chroot/unbound/db/
mkdir -p /var/chroot/unbound/etc/
chown unbound:unbound /var/chroot/unbound/db/
chmod 750 /var/chroot/unbound/db/
```
Optional – generate some key pairs for unbound-control to work
```
    unbound-control-setup
    ls -lF /usr/local/etc/unbound/unbound*.{key,pem}
```
Optional – otherwise using named pipe
```
    ls -lF /var/unbound.control.pipe
    mkfifo /var/unbound.control.pipe
```
Hints and trust anchor

Grab the one and only root anchor
```
    unbound-anchor -a /var/chroot/unbound/db/root.key
```
this file gets updated by the daemon itself (see auto-trust-anchor-file). But I am not sure we need to fix the ownership – maybe unbound takes care of it during startup already.

    chown unbound:unbound /var/chroot/unbound/db/root.key
    # daemon will revert it to 644 anyway
    #chmod 640 /var/chroot/unbound/db/root.key

Grab the root hints and signature

    cd /var/chroot/unbound/
    wget https://www.internic.net/domain/named.cache
    wget https://www.internic.net/domain/named.cache.sig

also import InterNIC’s PGP pubkey and verify the file

    gpg --version # 2.2.19
    gpg --keyserver pgp.mit.edu --receive-key 937BB869E3A238C5
    gpg --verify named.cache.sig
    # F0CB 1A32 6BDF 3F3E FA3A  01FA 937B B869 E3A2 38C5

It’s best the hints remain with root ownership, as those aren’t managed by the daemon.

Prepare a log file with the right permissions within the chroot

    touch /var/chroot/unbound/db/unbound.log
    chown unbound:unbound /var/chroot/unbound/db/unbound.log
    chmod 640 /var/chroot/unbound/db/unbound.log
    ln -s /var/chroot/unbound/db/unbound.log /var/log/unbound.log

Caching and validating resolver setup

We’ve got a chroot and we need to keep a consistent path with the host system

    mv /usr/local/etc/unbound/unbound.conf /usr/local/etc/unbound/unbound.con.dist
    cd /var/chroot/unbound/etc/
    grep -vE '^[[:space:]]*(#|$)' /usr/local/etc/unbound/unbound.conf.dist > unbound.conf

Handy symlinks

    ln -s /var/chroot/unbound/etc/unbound.conf ~/unbound.conf
    ln -s /var/chroot/unbound/etc/unbound.conf /etc/unbound.conf
    ln -s /var/chroot/unbound/etc/unbound.conf /usr/local/etc/unbound/unbound.conf

How many cores do you have? And proceed

    grep ^processor /proc/cpuinfo
    vi /etc/unbound.conf

    server:
            verbosity: 2
            num-threads: HOW_MANY_CORES
            interface: 0.0.0.0
            #interface: ::0
            access-control: 0.0.0.0/0 allow
            #access-control: ::/0 allow
            pidfile: "/var/run/unbound.pid" # not within chroot
            hide-identity: yes
            hide-version: yes
            #rrset-roundrobin: yes
            qname-minimisation: yes
            do-not-query-localhost: no

        username: "unbound"
        chroot: "/var/chroot/unbound"
        # pathes within chroot
        directory: "/"
        root-hints: "/named.cache"
        auto-trust-anchor-file: "/db/root.key"
        logfile: "/db/unbound.log"

    remote-control:
            control-enable: no

otherwise

    remote-control:
            control-enable: yes
            control-interface: /var/unbound.control.pipe

sample domain

assuming an authoritative server to bind to on alternate port 5353

            domain-insecure: "example.local"
            domain-insecure: "1.1.10.in-addr.arpa"
            #local-zone: "example.local" transparent
            #local-zone: "1.1.10.in-addr.arpa" transparent

    stub-zone:
            name: "example.local"
            stub-addr: ::1@5353

    stub-zone:
            name: "1.1.10.in-addr.arpa"
            stub-addr: ::1@5353

Ready to go (standalone)

read the logs

    tail -n50 -F /var/log/unbound.log

check the configuration

    unbound-checkconf /etc/unbound.conf

start and enable at boot time

    vi /etc/rc.local

    echo -n unbound...
    unbound -c /etc/unbound.conf && echo done || echo FAILED

status – main process runs as unbound user

    cat /var/run/unbound.pid
    pgrep -a unbound
    ps auxfww | grep unbound | grep -vE 'grep|tail'
    netstat -lntup | grep unbound

stop

    pkill unbound && rm -f /var/run/unbound.pid

Ready to go (remote control)

start and enable at boot time

    echo -n unbound-control...
    rm -f /var/unbound.control.pipe
    /usr/bin/mkfifo /var/unbound.control.pipe
    # same path within chroot
    unbound-control -c /etc/unbound.conf start && echo done || echo FAIL

you will notice the perms are updated by the daemon as such

    ls -lF /var/unbound.control.pipe

    srw-rw---- 1 unbound unbound 0 Oct  2 12:53 /var/unbound.control.pipe=

status (no need to enforce the config path here)

    unbound-control status
    unbound-control stats_noreset

reload (idem)

    unbound-control reload

Acceptance

locally

The managed dnssec root key is in da place (there’s no way to make it 640?)

    ls -lhF /var/chroot/unbound/db/root.key
    cat /var/chroot/unbound/db/root.key

Testing local-zone

    host localhost localhost
    host 127.0.0.1 localhost

Testing cached public zone

    host nethence.com localhost
    host 62.210.110.7 localhost

Testing cashed stub-zone

    host example.local localhost
    host INTERNAL-IP localhost

Tracing iterative queries

    dig nethence.com +trace @localhost

Testing DNSSEC (do and ad flags)

    dig nethence.com +dnssec @localhost

More options

    +multiline

remotely

    nmap -sU -p 53 DNS-SERVER
    nmap -p 53 DNS-SERVER

and re-iterate a few tests from above.
Maintenance

Flush the cache against a specific zone

    unbound-control flush_zone example.local

Flush the overall cache

    unbound-control reload

Analyze the cache

    unbound-control dump_cache > cache.dump
    less cache.dump

Easy family-shield forwarder

        #root-hints: "/named.cache"

        forward-zone:
                name: "."
        # OpenDNS Family Shield
                forward-addr: 208.67.222.123
                forward-addr: 208.67.220.123
        # CleanBrowsing Family Filter
                # 185.228.168.168
                # 185.228.169.168

acceptance

    host some-pr0n-website

should return

    146.112.61.106
    ::ffff:146.112.61.106
    106.61.112.146.in-addr.arpa domain name pointer hit-adult.opendns.com.

oh and when ever your browser connects there, it will get an un-trusted certificate

    srv=hit-adult.opendns.com
    port=443

    echo Q | openssl s_client -servername $srv -connect $srv:$port 2>&1 | openssl x509 -noout -text

Keep the cache upon reboot

BEFORE you kill the daemon

    vi /etc/rc.local_shutdown

    echo -n dumping dns cache...
    unbound-control dump_cache > /var/tmp/cache.dump && echo done || echo FAIL

    echo -n shutting down unbound...
    pkill unbound && echo done || echo FAIL

AFTER you start the daemon

    vi /etc/rc.local

    echo -n loading dns cache...
    unbound-control load_cache < /var/tmp/cache.dump && echo done || echo FAIL

it’s worth noting an empty cache db looks as such

    START_RRSET_CACHE
    END_RRSET_CACHE
    START_MSG_CACHE
    END_MSG_CACHE
    EOF

Troubleshooting

If Unbound service is listening but refusing to answer queries, fix access-control: as shown in the example above.

With verbosity 3, if you get

    configured stub servers failed -- returning SERVFAIL

==> check do-not-query-localhost

Against a stub zone too, if you get

    info: query response was nodata ANSWER

==> if it is not signed, domain-insecure helps
Resources

unbound.conf - Unbound configuration file. https://nlnetlabs.nl/documentation/unbound/unbound.conf/
build

Unbound-1.9.1 http://www.linuxfromscratch.org/blfs/view/svn/server/unbound.html
hints and anchors

Root Files https://www.iana.org/domains/root/files

Trust Anchors and Keys https://www.iana.org/dnssec/files

unbound-anchor - Unbound anchor utility. https://nlnetlabs.nl/documentation/unbound/unbound-anchor/

Howto enable DNSSEC https://nlnetlabs.nl/documentation/unbound/howto-anchor/
guides

Unbound https://wiki.archlinux.org/title/Unbound

unbound/doc/example.conf.in https://github.com/NLnetLabs/unbound/blob/master/doc/example.conf.in old http://unbound.nlnetlabs.nl/svn/trunk/doc/example.conf.in

[Unbound-users] reverse lookup stub zone https://www.unbound.net/pipermail/unbound-users/2009-May/000583.html
maintenance

Unbound DNS Server Cache Control https://abridge2devnull.com/posts/2016/03/unbound-dns-server-cache-control/

Unbound DNS Server Cache Control https://abridge2devnull.com/posts/2016/03/unbound-dns-server-cache-control/
dnssec

How to test and validate DNSSEC using dig command line https://www.cyberciti.biz/faq/unix-linux-test-and-validate-dnssec-using-dig-command-line/

A Short Practical Tutorial of dig, DNS and DNSSEC https://metebalci.com/blog/a-short-practical-tutorial-of-dig-dns-and-dnssec/

How To Test Recursive Server (So You Think You Are Validating) https://dnsinstitute.com/documentation/dnssec-guide/ch03s02.html

What are all the flags in a dig response? https://serverfault.com/questions/729025/what-are-all-the-flags-in-a-dig-response/729121
upon reboot

Preserving unbound cache across reboots https://misc.openbsd.narkive.com/gk9c44Ga/preserving-unbound-cache-across-reboots

[SOLVED] Can unbound dns server use persistent caching? https://bbs.archlinux.org/viewtopic.php?id=205494
family-shield

Configuring Unbound as a simple forwarding DNS server https://www.redhat.com/sysadmin/forwarding-dns-2

Configure DNS forwarding on Unbound https://learn.akamai.com/en-us/webhelp/enterprise-threat-protector/enterprise-threat-protector/GUID-50A942A0-B474-488E-9A79-3ED0E5E88226.html

8 Free DNS Services to Block Porn Sites without Installing Software https://www.raymond.cc/blog/how-to-block-pornographic-websites-without-spending-money-on-software/

HOW TO BLOCK PORN ON ANY DEVICE. FOR FREE. https://protectyoungeyes.com/how-to-block-porn-on-any-device-for-free/

Anyone know of a good blocklist for porn / adult content? https://www.reddit.com/r/pihole/comments/a6crei/anyone_know_of_a_good_blocklist_for_porn_adult/

5 DNS Services to Block Porn Sites without Installing Software https://jamiat.org.za/5-dns-services-to-block-porn-sites-without-installing-software/

How does DNS blacklisting work https://superuser.com/questions/1144929/how-does-dns-blacklisting-work –> DIY

About Blacklists https://docs.infoblox.com/display/NAG8/About+Blacklists –> DIY

Add porn blocking to your Pi-hole https://mangolassi.it/topic/16905/add-porn-blocking-to-your-pi-hole –> DIY
todo

Config for running Unbound as a caching DNS forwarder (performance settings optimized for Raspberry Pi 2). https://gist.github.com/MatthewVance/5051bf45cfed6e4a2a2ed9bb014bcd72 –> forward-tls-upstream: yes
```
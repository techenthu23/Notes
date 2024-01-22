---
title: Slow SSH Login
draft: false
---


On fresh installed Linux servers you might have encountered that it takes quite some time (approx. 30 sec) before you get to see the password prompt when you connect using SSH. As soon as you entered the password everything is normal.

There are two items that might cause this problem:

1. DNS Resolution
2. Unsupported Authentication methods

GSSAPI stands for Generic Security Services API and is a standard interface so SSH can communicate with Kerberos.

GSSAPI authentication, by itself, is generally fail-fast. That is, if your calling host isn’t attempting to present a GSSAPI token (i.e., “Kerberos”), the GSSAPI routines are generally skipped. If a token is presented, then, as part of the GSSAPI process, DNS is queried for A and PTR records. If DNS isn’t responsive, the connection attempt will pause for the DNS lookup attempt to time out

Solution

- Update /etc/ssh/sshd\_config and restart sshd

```bash
UseDNS no 
GSSAPIAuthentication no
```

- Try removing single-request-reopen from /etc/resolv.conf

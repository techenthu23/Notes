
- [NFT tables](#nft-tables)
  - [What are nftables?](#what-are-nftables)
  - [Use Cases](#use-cases)
  - [Nftables Families](#nftables-families)
  - [Components in nftables](#components-in-nftables)
    - [Tables](#tables)
    - [Chains](#chains)
      - [Base Chain](#base-chain)
        - [Base chain types](#base-chain-types)
        - [Base chain hooks](#base-chain-hooks)
        - [Base chain priority](#base-chain-priority)
        - [Base chain policy](#base-chain-policy)
      - [Regular Chain](#regular-chain)
    - [Rules](#rules)
  - [Key Configuration files](#key-configuration-files)
  - [Best Practices](#best-practices)
  - [Troubleshooting](#troubleshooting)
  - [Cheatsheet](#cheatsheet)
    - [Basic Commands](#basic-commands)
    - [Table Management](#table-management)
    - [Chain Management](#chain-management)
      - [1. Filter Chain](#1-filter-chain)
      - [2. NAT Chain](#2-nat-chain)
      - [3. Route Chain](#3-route-chain)
      - [4. Bridge Chain](#4-bridge-chain)
      - [5. Security Chain](#5-security-chain)
    - [Rule Management](#rule-management)
    - [NAT Rules](#nat-rules)
    - [Logging and Monitoring](#logging-and-monitoring)
    - [Troubleshooting](#troubleshooting-1)
    - [Basic Ruleset](#basic-ruleset)
    - [NAT Rules (If Needed)](#nat-rules-if-needed)
    - [Example Ruleset](#example-ruleset)
    - [Backup and Restore](#backup-and-restore)
    - [Atomic Rule Replacement](#atomic-rule-replacement)
    - [NFT Scripting](#nft-scripting)
  - [logging and monitoring in nftables](#logging-and-monitoring-in-nftables)
    - [Logging Traffic](#logging-traffic)
    - [Monitoring Traffic](#monitoring-traffic)
    - [Example Configuration](#example-configuration)
      - [Best Practices](#best-practices-1)
    - [Advance logging using ulogd](#advance-logging-using-ulogd)
      - [Step-by-Step Guide](#step-by-step-guide)
      - [Advanced Configuration](#advanced-configuration)
      - [Monitoring and Analysis](#monitoring-and-analysis)
      - [Example Configuration](#example-configuration-1)
      - [Best Practices](#best-practices-2)
    - [Configure ulogd to log specific events only](#configure-ulogd-to-log-specific-events-only)
      - [Step-by-Step Guide](#step-by-step-guide-1)
      - [Advanced Configuration](#advanced-configuration-1)
      - [Example Configuration](#example-configuration-2)
      - [Best Practices](#best-practices-3)
    - [Common Challenges](#common-challenges)
      - [Best Practices](#best-practices-4)
  - [References](#references)

# NFT tables

## What are nftables?

nftables is a Linux packet classification framework that replaces the Netfilter infrastructure behind iptables, ip6tables, arptables, and ebtables. Frameworks using the legacy Netfilter infrastructure are being phased out of the major Linux distributions. These frameworks have begun to adopt nftables as the default packet classification framework.

Despite the ubiquity of iptables, its architecture has several underlying limitations and inefficiencies, and these could only be resolved with a fundamental redesign. That redesign is what nftables set out to accomplish.

What are nftables?
nftables is a Linux packet classification framework that replaces the Netfilter infrastructure behind iptables, ip6tables, arptables, and ebtables. Frameworks using the legacy Netfilter infrastructure are being phased out of the major Linux distributions. These frameworks have begun to adopt nftables as the default packet classification framework.

Despite the ubiquity of iptables, its architecture has several underlying limitations and inefficiencies, and these could only be resolved with a fundamental redesign. That redesign is what nftables set out to accomplish.

## Use Cases

Nftables is a powerful and flexible framework for managing network packet filtering and classification. Here are some common use cases:

1. **Firewall Rules**:
   - **Purpose**: To control incoming and outgoing network traffic based on predefined security rules.
   - **Example**: Blocking specific IP addresses, allowing traffic only on certain ports, or setting up stateful firewalls.

2. **Network Address Translation (NAT)**:
   - **Purpose**: To modify network address information in packet headers while in transit.
   - **Example**: Implementing source NAT (SNAT) for outbound traffic or destination NAT (DNAT) for inbound traffic to redirect packets to different IP addresses or ports.

3. **Traffic Shaping and Rate Limiting**:
   - **Purpose**: To control the bandwidth usage and manage network congestion.
   - **Example**: Limiting the rate of incoming connections to prevent DDoS attacks or ensuring fair bandwidth distribution among users.

4. **Logging and Monitoring**:
   - **Purpose**: To keep track of network traffic for security and troubleshooting purposes.
   - **Example**: Logging dropped packets, monitoring connection attempts, or tracking bandwidth usage.

5. **Bridging Firewall**:
   - **Purpose**: To filter traffic between network bridges.
   - **Example**: Applying firewall rules to traffic passing through a bridge interface, useful in virtualized environments.

6. **IPv6 Support**:
   - **Purpose**: To manage and filter IPv6 traffic.
   - **Example**: Creating rules specific to IPv6 addresses and protocols, ensuring compatibility with modern networks.

7. **Custom Protocol Handling**:
   - **Purpose**: To create rules for less common or custom network protocols.
   - **Example**: Filtering traffic based on custom protocol headers or handling non-standard network services.

8. **Integration with Other Tools**:
   - **Purpose**: To enhance functionality by integrating with other network management tools.
   - **Example**: Using nftables with firewalld for easier rule management or integrating with monitoring tools like Prometheus.

These use cases highlight the versatility of nftables in managing and securing network traffic.

## Nftables Families  

Netfilter enables filtering at multiple networking levels. With iptables there is a separate tool for each level: iptables, ip6tables, arptables, ebtables. With nftables the multiple networking levels are abstracted into families, all of which are served by the single tool nft. The following are descriptions of current nftables families, but additional families may be added in the future.

1. **ip**
Tables of this family see IPv4 traffic/packets. The iptables tool is the legacy x_tables equivalent.
2. **ip6**
Tables of this family see IPv6 traffic/packets. The ip6tables tool is the legacy x_tables equivalent.
3. **inet**
Tables of this family see both IPv4 and IPv6 traffic/packets, simplifying dual stack support.
4. **arp**
Tables of this family see ARP-level (i.e, L2) traffic, before any L3 handling is done by the kernel. The arptables tool is the legacy x_tables equivalent.
5. **bridge**
Tables of this family see traffic/packets traversing bridges (i.e. switching). No assumptions are made about L3 protocols.
6. **netdev**
The netdev family is different from the others in that it is used to create base chains attached to a single network interface. Such base chains see all network traffic on the specified interface, with no assumptions about L2 or L3 protocols. Therefore you can filter ARP traffic from here. There is no legacy x_tables equivalent to the netdev family.

## Components in nftables

### Tables

Tables are the highest level of the nftables hierarchy. A given table corresponds to a single address family ( such as inet, ip, ip6, arp, bridge, netdev ) and contains chains that filter packets in that address family.

### Chains

Chains live under tables and filter packets. You attach each nftables rule to a chain so that packets “caught” in the chain’s filter are subsequently passed to the chain’s rules.  

Chains can be of two kinds.

- **Base chains** act as entry points for packets coming from the network stack.
- **Regular chains** do not act as filters, but can act as jump targets. They can help with controlling the flow and organization of your nftables.

#### Base Chain

##### Base chain types

The possible chain types are:

- **filter**, which is used to filter packets. This is supported by the arp, bridge, ip, ip6 and inet table families.
- **route**, which is used to reroute packets if any relevant IP header field or the packet mark is modified. If you are familiar with iptables, this chain type provides equivalent semantics to the mangle table but only for the output hook (for other hooks use type filter instead). This is supported by the ip, ip6 and inet table families.
- **na**t, which is used to perform Networking Address Translation (NAT). Only the first packet of a given flow hits this chain; subsequent packets bypass it. Therefore, never use this chain for filtering. The nat chain type is supported by the ip, ip6 and inet table families.

##### Base chain hooks

The possible hooks that you can use when you configure your base chain are:

1. **ingress** (only in netdev family since Linux kernel 4.2, and inet family since Linux kernel 5.10): sees packets immediately after they are passed up from the NIC driver, before even prerouting. So you have an alternative to tc.
2. **prerouting**: sees all incoming packets, before any routing decision has been made. Packets may be addressed to the local or remote systems.
3. **input**: sees incoming packets that are addressed to and have now been routed to the local system and processes running there.
4. **forward**: sees incoming packets that are not addressed to the local system.
5. **output**: sees packets that originated from processes in the local machine.
6. **postrouting**: sees all packets after routing, just before they leave the local system.

##### Base chain priority

Each nftables base chain is assigned a priority that defines its ordering among other base chains, flowtables, and Netfilter internal operations at the same hook. For example, a chain on the prerouting hook with priority -300 will be placed before connection tracking operations.

> **NOTE**: *If a packet is accepted and there is another chain, bearing the same hook type and with a later priority, then the packet will subsequently traverse this other chain. Hence, an accept verdict - be it by way of a rule or the default chain policy - isn't necessarily final. However, the same is not true of packets that are subjected to a drop verdict. Instead, drops take immediate effect, with no further rules or chains being evaluated.*

##### Base chain policy

This is the default verdict that will be applied to packets reaching the end of the chain (i.e, no more rules to be evaluated against).

Currently there are 2 policies: **accept** (default) or **drop**.

- The accept verdict means that the packet will keep traversing the network stack (default).
- The drop verdict means that the packet is discarded if the packet reaches the end of the base chain.

> NOTE: If no policy is explicitly selected, the default policy accept will be used.

#### Regular Chain

A regular chain lacks the portion with the type, hook, and priority. Because regular chains lack hooks, they do not receive packets automatically. Instead, they rely on rules using the jump or goto action to relay packets to them. When this occurs, the regular chain processes packets just as a base chain does.

Generally, the point in doing this is organizational. You can create a “tree” of base and regular chains, allowing you to control the arrangement and flow of your nftables ruleset.

### Rules

Rules receive the packets filtered by chains and take actions on them based on whether they match particular criteria. Each rule consists of two parts, which follow the table and chain in the command. First, the rule has zero or more expressions which give the criteria for the rule. Second, the rule has one or more statements which determine the action or actions taken when a packet matches the rule’s expressions. Both expressions and statements are evaluated from left to right.

Packets progress through a chain’s rules from beginning to end and cease to be processed when they match and are acted on.

## Key Configuration files

some key configuration files related to **nftables** and their purposes:

1. **/etc/nftables.conf**:
   - **Purpose**: This is the main configuration file for nftables. It contains the ruleset that nftables will load when the service starts. You can define tables, chains, and rules in this file to manage network traffic.

2. **/etc/sysconfig/nftables.conf** (on some distributions):
   - **Purpose**: Similar to /etc/nftables.conf, this file is used to store the nftables ruleset. It is specific to certain Linux distributions like Red Hat-based systems.

3. **/usr/lib/nftables/policies.nft**:
   - **Purpose**: This file contains default policies for nftables. It can be included in the main configuration file to apply standard policies across different tables and chains.

4. **/etc/nftables.d/**:
   - **Purpose**: This directory can be used to store additional configuration files. You can split your nftables rules into multiple files for better organization and include them in the main configuration file.

5. **/etc/firewalld/zones/** (when using firewalld with nftables backend):
   - **Purpose**: These files define firewall zones and their associated rules. Firewalld can use nftables as its backend, and these zone files help manage network traffic based on predefined zones.

6. **/etc/nftables/blacklist.nft**:
   - **Purpose**: This file can be used to store IP addresses or ranges that you want to block. It can be included in the main configuration file to apply blacklist rules.

These files help you manage and organize your nftables rulesets effectively. If you have any specific questions about configuring nftables or need help with a particular setup, feel free to ask!

## Best Practices

Managing firewall rules with nftables effectively requires careful planning and organization. Here are some best practices to consider:

1. **Plan Your Ruleset**:
   - **Purpose**: Define your security requirements and network policies before implementing rules.
   - **Tip**: Create a flowchart or diagram to visualize the traffic flow and identify key points where rules are needed.

2. **Use Descriptive Comments**:
   - **Purpose**: Make your ruleset easier to understand and maintain.
   - **Tip**: Add comments to each rule explaining its purpose and any relevant details.

3. **Organize Rules into Tables and Chains**:
   - **Purpose**: Keep your ruleset modular and manageable.
   - **Tip**: Use separate tables for different types of traffic (e.g., filter, nat) and chains for specific tasks (e.g., input, output, forward).

4. **Implement Default Policies**:
   - **Purpose**: Ensure a secure baseline by setting default policies for each chain.
   - **Tip**: Set default policies to drop or reject traffic unless explicitly allowed.

5. **Test Rules in a Safe Environment**:
   - **Purpose**: Avoid disruptions by testing new rules in a controlled setting.
   - **Tip**: Use a staging environment or a virtual machine to test rules before applying them to production.

6. **Use Atomic Rule Updates**:
   - **Purpose**: Prevent partial rule updates that could leave your firewall in an inconsistent state.
   - **Tip**: Use the `nft` command's atomic replace feature to apply all changes at once. Restarting nftables can temporarily disable the prerouting hook, affecting NAT and port forwarding.

7. **Monitor and Log Traffic**:
   - **Purpose**: Keep track of network activity and identify potential issues.
   - **Tip**: Enable logging for critical rules and regularly review log files.

8. **Regularly Review and Update Rules**:
   - **Purpose**: Ensure your firewall rules remain relevant and effective.
   - **Tip**: Schedule periodic reviews of your ruleset to remove obsolete rules and add new ones as needed.

9. **Backup Your Configuration**:
   - **Purpose**: Protect against accidental loss or corruption of your ruleset.
   - **Tip**: Regularly back up your nftables configuration files and store them in a secure location.

10. **Use Include Files for Large Rulesets**:
    - **Purpose**: Improve readability and manageability of complex rulesets.
    - **Tip**: Split large rulesets into multiple files and use the `include` directive to combine them.

By following these best practices, you can create a robust and maintainable nftables firewall configuration.

When working with nftables, it's important to be aware of common pitfalls to ensure your firewall rules are effective and secure. Here are some mistakes to avoid:

1. **Not Setting Default Policies**:
   - **Mistake**: Failing to set default policies for chains can leave your system vulnerable.
   - **Solution**: Always set default policies to drop or reject traffic unless explicitly allowed.

2. **Overly Permissive Rules**:
   - **Mistake**: Creating rules that are too broad can unintentionally allow unwanted traffic.
   - **Solution**: Be specific with your rules, defining precise IP addresses, ports, and protocols.

3. **Lack of Testing**:
   - **Mistake**: Applying rules directly to a production environment without testing can cause disruptions.
   - **Solution**: Test new rules in a staging environment or on a virtual machine first.

4. **Ignoring Logging**:
   - **Mistake**: Not enabling logging can make it difficult to troubleshoot issues or detect malicious activity.
   - **Solution**: Enable logging for critical rules and regularly review log files.

5. **Complex and Unorganized Rulesets**:
   - **Mistake**: Having a disorganized or overly complex ruleset can make management difficult and error-prone.
   - **Solution**: Organize rules into tables and chains, use descriptive comments, and consider using include files for large rulesets.

6. **Not Updating Rules Regularly**:
   - **Mistake**: Failing to review and update rules can leave your firewall outdated and ineffective.
   - **Solution**: Schedule regular reviews of your ruleset to remove obsolete rules and add new ones as needed.

7. **Forgetting to Backup Configuration**:
   - **Mistake**: Not backing up your nftables configuration can lead to data loss in case of accidental changes or system failures.
   - **Solution**: Regularly back up your configuration files and store them securely.

8. **Misconfiguring NAT Rules**:
   - **Mistake**: Incorrectly setting up NAT rules can lead to connectivity issues.
   - **Solution**: Double-check your NAT rules and ensure they are correctly applied to the appropriate interfaces and addresses.

9. **Ignoring IPv6 Traffic**:
   - **Mistake**: Overlooking IPv6 traffic can leave your network partially unprotected.
   - **Solution**: Ensure your ruleset includes provisions for both IPv4 and IPv6 traffic.

10. **Not Using Atomic Rule Updates**:
    - **Mistake**: Applying rules one at a time can leave your firewall in an inconsistent state during updates.
    - **Solution**: Use the atomic replace feature of the `nft` command to apply all changes at once.

By avoiding these common mistakes, you can ensure your nftables configuration is robust, secure, and effective. If you have any specific questions or need further guidance, feel free to ask!

## Troubleshooting

Recovering from a misconfigured nftables ruleset can be critical to restoring network functionality. Here are some steps you can follow:

1. **Flush Current Rules**:
   - **Purpose**: Remove all current rules to start with a clean slate.
   - **Command**: `sudo nft flush ruleset`

2. **Load a Known Good Configuration**:
   - **Purpose**: Replace the misconfigured ruleset with a previously saved, working configuration.
   - **Command**: `sudo nft -f /path/to/backup/nftables.conf`

3. **Use a Minimal Ruleset**:
   - **Purpose**: Apply a basic ruleset that allows essential traffic while you troubleshoot the issue.
   - **Example**:

     ```nft
     table inet minimal {
         chain input {
             type filter hook input priority 0; policy accept;
         }
         chain forward {
             type filter hook forward priority 0; policy accept;
         }
         chain output {
             type filter hook output priority 0; policy accept;
         }
     }
     ```

   - **Command**: Save the above ruleset to a file (e.g., `minimal.nft`) and load it using `sudo nft -f minimal.nft`.

4. **Check for Syntax Errors**:
   - **Purpose**: Ensure there are no syntax errors in your ruleset.
   - **Command**: `sudo nft -c -f /path/to/your/nftables.conf`
   - **Tip**: The `-c` flag checks the configuration without applying it.

5. **Review Logs**:
   - **Purpose**: Identify any errors or warnings related to nftables.
   - **Command**: `sudo journalctl -xe | grep nftables`

6. **Restore from Backup**:
   - **Purpose**: If you have a backup of your nftables configuration, restore it.
   - **Command**: `sudo cp /path/to/backup/nftables.conf /etc/nftables.conf && sudo systemctl restart nftables`

7. **Use Safe Mode**:
   - **Purpose**: Temporarily allow all traffic to ensure connectivity while you fix the ruleset.
   - **Command**:

     ```nft
     table inet safe_mode {
         chain input {
             type filter hook input priority 0; policy accept;
         }
         chain forward {
             type filter hook forward priority 0; policy accept;
         }
         chain output {
             type filter hook output priority 0; policy accept;
         }
     }
     ```

   - **Command**: Save the above ruleset to a file (e.g., `safe_mode.nft`) and load it using `sudo nft -f safe_mode.nft`.

8. **Seek Help**:
   - **Purpose**: If you're unable to resolve the issue, seek assistance from community forums or professional support.
   - **Tip**: Provide detailed information about your configuration and the issues you're facing.

By following these steps, you can recover from a misconfigured nftables ruleset and restore normal network operations.  

Here are some common troubleshooting commands for nftables that can help you diagnose and resolve issues:

1. **List Current Ruleset**:
   - **Command**: `sudo nft list ruleset`
   - **Purpose**: Displays the entire current nftables ruleset, including tables, chains, and rules.

2. **Check for Syntax Errors**:
   - **Command**: `sudo nft -c -f /path/to/your/nftables.conf`
   - **Purpose**: Validates the syntax of your nftables configuration file without applying it.

3. **Flush Ruleset**:
   - **Command**: `sudo nft flush ruleset`
   - **Purpose**: Removes all current rules, useful for starting fresh or troubleshooting issues.

4. **Monitor Events**:
   - **Command**: `sudo nft monitor`
   - **Purpose**: Monitors and displays changes to the nftables ruleset in real-time.

5. **List Tables**:
   - **Command**: `sudo nft list tables`
   - **Purpose**: Lists all tables currently defined in nftables.

6. **List Chains in a Table**:
   - **Command**: `sudo nft list table <family> <table>`
   - **Purpose**: Lists all chains within a specified table.
   - **Example**: `sudo nft list table inet filter`

7. **List Rules in a Chain**:
   - **Command**: `sudo nft list chain <family> <table> <chain>`
   - **Purpose**: Lists all rules within a specified chain.
   - **Example**: `sudo nft list chain inet filter input`

8. **Check Kernel Modules**:
   - **Command**: `sudo lsmod | grep nf_tables`
   - **Purpose**: Checks if nftables kernel modules are loaded.

9. **View Logs**:
   - **Command**: `sudo journalctl -xe | grep nftables`
   - **Purpose**: Reviews system logs for nftables-related messages and errors.

10. **Export Current Ruleset**:
    - **Command**: `sudo nft list ruleset > /path/to/backup/nftables-backup.conf`
    - **Purpose**: Saves the current ruleset to a file for backup or review.

These commands can help you effectively troubleshoot and manage your nftables configuration. If you encounter specific issues or need further assistance, feel free to ask!

Source: Conversation with Copilot, 16/8/2024

## Cheatsheet

Here's a handy nftables command cheatsheet to help you manage and troubleshoot your firewall rules:

### Basic Commands

- **Install nftables**

  ```sh
    sudo yum install nftables
    sudo systemctl enable nftables
    sudo systemctl start nftables

    sudo yum install iptables-nftables-compat
    sudo iptables-save > iptables.dump
    sudo iptables-restore-translate -f iptables.dump > ruleset.nft
    sudo nft -f ruleset.nft
  ```

- **List Current Ruleset**:

  ```sh
  sudo nft list ruleset
  ```

- **Flush Current Ruleset**:

  ```sh
  sudo nft flush ruleset
  ```

- **Load Ruleset from File**:

  ```sh
  sudo nft -f /path/to/your/nftables.conf
  ```

- **Save Current Ruleset to File**:

  ```sh
  sudo nft list ruleset > /path/to/backup/nftables-backup.conf
  ```

### Table Management

- **List Tables**:

  ```sh
  sudo nft list tables
  ```

- **Add Table**:

  ```sh
  sudo nft add table <family> <table_name>
  ```

- **Delete Table**:

  ```sh
  sudo nft delete table <family> <table_name>
  ```

### Chain Management

- **List Chains in a Table**:

  ```sh
  sudo nft list table <family> <table_name>
  ```

- **Add Chain**:

   Base Chain

  ```sh
  sudo nft add chain <family_type> <table_name> <chain_name> '{ type <chain_type> hook <hook_type> priority <priority_value> \; }'
  ```

For IPv4/IPv6/Inet address families hook_type can be prerouting, input, forward, output, or postrouting

   Regular Chain

   ```sh
   sudo nft add chain <family_type> <table_name> <chain_name>
   ```

- **Delete Chain**:

  ```sh
  sudo nft delete chain <family> <table_name> <chain_name>
  ```

In nftables, chains are used to define sets of rules that can be applied to network packets. There are different types of chains, each serving a specific purpose. Here are examples of defining different types of chains in nftables:

#### 1. Filter Chain

- **Purpose**: Used to filter packets based on various criteria.
- **Example**:

     ```sh
     sudo nft add table inet filter
     sudo nft add chain inet filter input { type filter hook input priority 0 \; policy accept \; }
     sudo nft add rule inet filter input tcp dport 22 accept
     ```

- **Explanation**: This example creates a filter chain in the `inet` table, hooks it to the input, and accepts incoming SSH traffic on port 22.

#### 2. NAT Chain

- **Purpose**: Used for Network Address Translation (NAT) to modify packet addresses.
- **Example**:

     ```sh
     sudo nft add table ip nat
     sudo nft add chain ip nat prerouting { type nat hook prerouting priority -100 \; }
     sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }
     sudo nft add rule ip nat postrouting oifname "eth0" masquerade
     ```

- **Explanation**: This example creates a NAT table with `prerouting` and `postrouting` chains, and applies masquerading on outgoing traffic through `eth0`.

#### 3. Route Chain

- **Purpose**: Used to reroute packets if any relevant IP header field or the packet mark is modified.
- **Example**:

     ```sh
     sudo nft add table ip route
     sudo nft add chain ip route prerouting { type route hook prerouting priority -150 \; }
     sudo nft add rule ip route prerouting ip daddr 192.168.1.1 drop
     ```

- **Explanation**: This example creates a route chain in the `ip` table, hooks it to the `prerouting`, and drops packets destined for `192.168.1.1`.

#### 4. Bridge Chain

- **Purpose**: Used for filtering packets on a bridge.
- **Example**:

     ```sh
     sudo nft add table bridge filter
     sudo nft add chain bridge filter forward { type filter hook forward priority 0 \; }
     sudo nft add rule bridge filter forward ether type arp drop
     ```

- **Explanation**: This example creates a filter chain in the `bridge` table, hooks it to the `forward`, and drops ARP packets.

#### 5. Security Chain

- **Purpose**: Used for security-related packet filtering.
- **Example**:

     ```sh
     sudo nft add table inet security
     sudo nft add chain inet security input { type filter hook input priority 0 \; policy accept \; }
     sudo nft add rule inet security input tcp dport 80 drop
     ```

- **Explanation**: This example creates a security chain in the `inet` table, hooks it to the `input`, and drops incoming HTTP traffic on port 80.

### Rule Management

    - **List Rules in a Chain**:

  ```sh
  sudo nft list chain <family> <table_name> <chain_name>
  ```

- **Add Rule**:

  ```sh
  sudo nft add rule <family> <table_name> <chain_name> <rule_specification>
  ```

- **Delete Rule by Handle**:

  ```sh
  sudo nft delete rule <family> <table_name> <chain_name> handle <handle_number> <statement>
  ```

Since removing a rule requires knowing its handle, removing rules is a two-step process.

- Determine handle
- Enter remove rule command

```sh
sudo nft list table mytable -a
sudo nft delete rule mytable input handle 2
```

### NAT Rules

- **Add SNAT Rule**:

  ```sh
  sudo nft add rule ip nat postrouting oif <interface> snat <source_ip>
  ```

- **Add DNAT Rule**:

  ```sh
  sudo nft add rule ip nat prerouting iif <interface> dnat <destination_ip>
  ```

### Logging and Monitoring

- **Enable Logging for a Rule**:

  ```sh
  sudo nft add rule <family> <table_name> <chain_name> log prefix "nftables: " flags all
  ```

- **Monitor Changes**:

  ```sh
  sudo nft monitor
  ```

### Troubleshooting

- **Check for Syntax Errors**:

  ```sh
  sudo nft -c -f /path/to/your/nftables.conf
  ```

- **View Logs**:

  ```sh
  sudo journalctl -xe | grep nftables
  ```

Setting up nftables rules for an internet-facing machine involves creating a secure and efficient firewall configuration. Here are some essential rules and best practices:

### Basic Ruleset

1. **Flush Existing Rules**:

   ```sh
   sudo nft flush ruleset
   ```

2. **Define Tables and Chains**:

   ```sh
   sudo nft add table inet filter
   sudo nft add chain inet filter input { type filter hook input priority 0 \; policy drop \; }
   sudo nft add chain inet filter forward { type filter hook forward priority 0 \; policy drop \; }
   sudo nft add chain inet filter output { type filter hook output priority 0 \; policy accept \; }
   ```

3. **Allow Loopback Traffic**:

   ```sh
   sudo nft add rule inet filter input iif lo accept
   ```

4. **Allow Established and Related Traffic**:

   ```sh
   sudo nft add rule inet filter input ct state established,related accept
   ```

5. **Allow ICMP (Ping) for Diagnostics**:

   ```sh
   sudo nft add rule inet filter input icmp type echo-request accept
   ```

6. **Allow SSH Access**:

   ```sh
   sudo nft add rule inet filter input tcp dport 22 ct state new accept
   ```

7. **Drop Invalid Packets**:

   ```sh
   sudo nft add rule inet filter input ct state invalid drop
   ```

8. **Log Dropped Packets (Optional)**:

   ```sh
   sudo nft add rule inet filter input log prefix "nftables: " flags all counter drop
   ```

### NAT Rules (If Needed)

1. **Define NAT Table and Chains**:

   ```sh
   sudo nft add table ip nat
   sudo nft add chain ip nat prerouting { type nat hook prerouting priority 0 \; }
   sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }
   ```

2. **Add SNAT Rule**:

   ```sh
   sudo nft add rule ip nat postrouting oif <interface> snat <source_ip>
   ```

3. **Add DNAT Rule**:

   ```sh
   sudo nft add rule ip nat prerouting iif <interface> dnat <destination_ip>
   ```

### Example Ruleset

Here's an example of a complete ruleset for an internet-facing machine:

```nft
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iif lo accept
        ct state established,related accept
        icmp type echo-request accept
        tcp dport 22 ct state new accept
        ct state invalid drop
        log prefix "nftables: " flags all counter drop
    }
    chain forward {
        type filter hook forward                            priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}

table ip nat {
    chain prerouting {
        type nat hook prerouting priority 0;
    }
    chain postrouting {
        type nat hook postrouting priority 100;
        oif <interface> snat <source_ip>
    }
}
```

Replace `<interface>` and `<source_ip>` with your actual network interface and source IP address.

### Backup and Restore

You can combine these two commands above to backup your ruleset:

```sh
sudo echo "NFT Ruleset Backup - $(date)" > backup.nft
sudo nft list ruleset >> backup.nft
```

And load it atomically:

```sh
sudo nft -f backup.nft

```

By following these guidelines, you can set up a secure and efficient nftables configuration for your internet-facing machine.

### Atomic Rule Replacement

You can use the -f option to atomically update your ruleset:

`% nft -f file`

Where file contains your ruleset.

You can save your ruleset by storing the existing listing in a file, ie.

`% nft list table filter > filter-table`

Then you can restore it by using the -f option:

`% nft -f filter-table`

Notes :  Please, take these notes into consideration:

- **Table Creation**: you may have to create the table with nft create table ip filter before you can load the file exported with nft list table filter > filter-table otherwise you will hit errors because the table does not exist. Newer nftables releases behave with more consistency regarding this.
- **Duplicate Rules**: If you prepend the flush table filter line at the very beginning of the filter-table file, you achieve atomic ruleset replacement equivalent to what iptables-restore provides. The kernel handles the rule commands in the file in one single transaction, so basically the flushing and the load of the new rules happens in one single shot. If you choose not to flush your tables then you will see duplicate rules for each time you reloaded the config.
- **Flushing Sets**: flush table filter will not flush any sets defined in that table. To flush sets as well, use flush ruleset (available since Linux 3.17 ) or delete the sets explicitly. Early versions (Linux <=3.16) do not allow you to import a set if it already exists, but this is allowed in later versions.
- **What happens when you include 2 files which each have a statement for the filter table?** If you have two included files both with statements for the filter table, but one adds a rule allowing traffic from 192.168.1.1 and the other allows traffic from 192.168.1.2 then both rules will be included in the chain, even if one or both files contains a flush statement.
- **What about flush statements in either, or neither file?** If there are any flush commands in any included file then those will be run at the moment the config swap is executed, not at the moment the file is loaded. If you do not include a flush statement in any included file, you will get duplicate rules. If you do include a flush statement, you will not get duplicate rules and the config from *both* files will be included.

### NFT Scripting

nftables provides a native scripting environment to include other ruleset files, define variables and add comments. You have to restore the content of this native script through the nft -f my-ruleset.file command.

`nft -f <filename>` accepts 2 formats, the first is the format seen in the output of nft list table. The second is using the same syntax of calling the nft binary several times, but in an atomic fashion.

```sh
#!/usr/sbin/nft -f

# table declaration
add table filter


# chain declaration
add chain filter input { type filter hook input priority 0; policy drop; }


# rule declaration
add rule filter input ct state established,related counter accept

# include a single file using the default search path
include "ipv4-nat.ruleset"

# include all files ending in *.nft in the default search path
include "*.nft"

# include all files in a given directory using an absolute path
include "/etc/nftables/*"

# define variables

define google_dns = 8.8.8.8

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $google_dns counter

# define a variable for sets:
define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $ntp_servers counter
```

## logging and monitoring in nftables

Configuring logging and monitoring in nftables is essential for tracking network activity and diagnosing issues. Here’s how you can set it up:

### Logging Traffic

1. **Basic Logging Rule**:
   - **Command**:

     ```sh
     sudo nft add rule inet filter input log prefix "nftables: " flags all
     ```

   - **Purpose**: Logs all incoming traffic with a specified prefix.

2. **Logging Specific Traffic**:
   - **Example**: Log and accept new SSH connections.

     ```sh
     sudo nft add rule inet filter input tcp dport 22 ct state new log prefix "New SSH connection: " accept
     ```

3. **Log Flags**:
   - **Command**:

     ```sh
     sudo nft add rule inet filter input log flags all
     ```

   - **Purpose**: Enables logging of additional information such as TCP sequence numbers, IP options, and more¹.

4. **Queueing Logs to Userspace**:
   - **Command**:

     ```sh
     sudo nft add rule inet filter input tcp dport 22 ct state new log prefix "New SSH connection: " group 0 accept
     ```

   - **Purpose**: Sends log messages to a userspace application like `ulogd` for further processing¹.

### Monitoring Traffic

1. **Real-Time Monitoring**:
   - **Command**:

     ```sh
     sudo nft monitor
     ```

   - **Purpose**: Monitors and displays real-time changes and traffic passing through nftables.

2. **Trace Specific Rules**:
   - **Command**:

     ```sh
     sudo nft add rule inet filter input meta nftrace set 1
     sudo nft monitor trace
     ```

   - **Purpose**: Enables tracing for specific rules to debug and analyze packet flow⁴.

3. **View Counters**:
   - **Command**:

     ```sh
     sudo nft list counters
     ```

   - **Purpose**: Displays the counters for rules, useful for monitoring traffic statistics.

### Example Configuration

Here’s an example configuration that includes logging and monitoring:

```nft
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iif lo accept
        ct state established,related accept
        tcp dport 22 ct state new log prefix "New SSH connection: " accept
        log prefix "nftables: " flags all
        meta nftrace set 1
        counter drop
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```

#### Best Practices

- **Use Descriptive Prefixes**: Add meaningful prefixes to log entries to make them easier to identify.
- **Monitor Regularly**: Regularly monitor logs and counters to stay informed about network activity.
- **Integrate with Logging Tools**: Use tools like `ulogd` or `syslog` for advanced log management and analysis.

By following these steps, you can effectively configure logging and monitoring in nftables to enhance your network security and troubleshooting capabilities.

¹: [Logging traffic - nftables wiki](https://wiki.nftables.org/wiki-nftables/index.php/Logging_traffic)
⁴: [Ruleset debug/tracing - nftables wiki](https://wiki.nftables.org/wiki-nftables/index.php/Ruleset_debug/tracing)

### Advance logging using ulogd

Certainly! Using **ulogd** with **nftables** allows for advanced logging and analysis by sending log data to userspace, where it can be processed and stored in various formats. Here's how you can set it up:

#### Step-by-Step Guide

1. **Install ulogd**:
   - **Command**:

     ```sh
     sudo apt-get install ulogd2
     ```

   - **Purpose**: Installs the ulogd2 daemon, which is required for advanced logging.

2. **Configure ulogd**:
   - **Edit Configuration File**:

     ```sh
     sudo nano /etc/ulogd.conf
     ```

   - **Example Configuration**:

     ```ini
     [global]
     loglevel=5
     logfile="/var/log/ulogd.log"

     [log2]
     group=2

     [emu1]
     file="/var/log/nftables.log"
     sync=1
     ```

   - **Purpose**: Configures ulogd to log to a specific file and sets the logging group.

3. **Enable and Start ulogd**:
   - **Command**:

     ```sh
     sudo systemctl enable ulogd
     sudo systemctl start ulogd
     ```

   - **Purpose**: Ensures that ulogd starts on boot and begins logging immediately.

4. **Configure nftables to Use ulogd**:
   - **Example Rule**:

     ```sh
     sudo nft add rule inet filter input tcp dport 22 ct state new log prefix "New SSH connection: " group 2 accept
     ```

   - **Purpose**: Sends log messages to ulogd for new SSH connections.

#### Advanced Configuration

1. **Logging to a Database**:
   - **Edit Configuration File**:

     ```ini
     [mysql1]
     db="ulogd"
     host="localhost"
     user="ulogd"
     pass="password"
     table="ulog"
     ```

   - **Purpose**: Configures ulogd to log to a MySQL database.

2. **Using Different Output Formats**:
   - **Example Configuration**:

     ```ini
     [csv1]
     file="/var/log/nftables.csv"
     sync=1
     ```

   - **Purpose**: Logs data in CSV format for easy analysis.

#### Monitoring and Analysis

1. **Real-Time Monitoring**:
   - **Command**:

     ```sh
     sudo tail -f /var/log/nftables.log
     ```

   - **Purpose**: Monitors log entries in real-time.

2. **Analyzing Logs**:
   - **Command**:

     ```sh
     sudo cat /var/log/nftables.log | grep "New SSH connection"
     ```

   - **Purpose**: Filters log entries to find specific events.

#### Example Configuration

Here’s a complete example of a ulogd configuration for logging nftables traffic:

```ini
[global]
loglevel=5
logfile="/var/log/ulogd.log"

[log2]
group=2

[emu1]
file="/var/log/nftables.log"
sync=1

[mysql1]
db="ulogd"
host="localhost"
user="ulogd"
pass="password"
table="ulog"

[csv1]
file="/var/log/nftables.csv"
sync=1
```

#### Best Practices

- **Use Descriptive Prefixes**: Add meaningful prefixes to log entries to make them easier to identify.
- **Regularly Monitor Logs**: Keep an eye on log files to stay informed about network activity.
- **Integrate with Analysis Tools**: Use tools like `ulogd` with databases or log analysis software for deeper insights.

By following these steps, you can set up advanced logging and analysis with ulogd and nftables, enhancing your network security and troubleshooting capabilities.

### Configure ulogd to log specific events only

To configure **ulogd** to log specific events only, you need to set up nftables rules to send specific types of traffic to ulogd and then configure ulogd to handle those logs. Here’s how you can do it:

#### Step-by-Step Guide

1. **Install ulogd**:
   - **Command**:

     ```sh
     sudo apt-get install ulogd2
     ```

   - **Purpose**: Installs the ulogd2 daemon, which is required for advanced logging.

2. **Configure nftables to Log Specific Events**:
   - **Example Rule**: Log new SSH connections only.

     ```sh
     sudo nft add rule inet filter input tcp dport 22 ct state new log prefix "New SSH connection: " group 1 accept
     ```

   - **Purpose**: Sends log messages for new SSH connections to ulogd group 1.

3. **Edit ulogd Configuration**:
   - **Configuration File**:

     ```sh
     sudo nano /etc/ulogd.conf
     ```

   - **Example Configuration**:

     ```ini
     [global]
     loglevel=5
     logfile="/var/log/ulogd.log"

     [log1]
     group=1

     [emu1]
     file="/var/log/nftables.log"
     sync=1
     ```

   - **Purpose**: Configures ulogd to log messages from group 1 to a specific file.

4. **Enable and Start ulogd**:
   - **Command**:

     ```sh
     sudo systemctl enable ulogd
     sudo systemctl start ulogd
     ```

   - **Purpose**: Ensures that ulogd starts on boot and begins logging immediately.

#### Advanced Configuration

1. **Filter Specific Events**:
   - **Example**: Log only new TCP connections on port 80.

     ```sh
     sudo nft add rule inet filter input tcp dport 80 ct state new log prefix "New HTTP connection: " group 2 accept
     ```

   - **ulogd Configuration**:

     ```ini
     [log2]
     group=2

     [emu2]
     file="/var/log/http_connections.log"
     sync=1
     ```

   - **Purpose**: Logs new HTTP connections to a separate file.

2. **Logging to a Database**:
   - **Example Configuration**:

     ```ini
     [mysql1]
     db="ulogd"
     host="localhost"
     user="ulogd"
     pass="password"
     table="ulog"
     ```

   - **Purpose**: Configures ulogd to log to a MySQL database for advanced analysis.

#### Example Configuration

Here’s a complete example of a ulogd configuration for logging specific nftables events:

```ini
[global]
loglevel=5
logfile="/var/log/ulogd.log"

[log1]
group=1

[emu1]
file="/var/log/nftables.log"
sync=1

[log2]
group=2

[emu2]
file="/var/log/http_connections.log"
sync=1

[mysql1]
db="ulogd"
host="localhost"
user="ulogd"
pass="password"
table="ulog"
```

#### Best Practices

- **Use Descriptive Prefixes**: Add meaningful prefixes to log entries to make them easier to identify.
- **Regularly Monitor Logs**: Keep an eye on log files to stay informed about network activity.
- **Integrate with Analysis Tools**: Use tools like `ulogd` with databases or log analysis software for deeper insights.

By following these steps, you can configure ulogd to log specific events only, enhancing your network security and troubleshooting capabilities.

Set Up Logging Rule to Log traffic between localhost and a specific IP.

```sh
sudo nft add table inet filter
sudo nft add chain inet filter input { type filter hook input priority 0 \; policy accept \; }
sudo nft add chain inet filter output { type filter hook output priority 0 \; policy accept \; }    
sudo nft add rule inet filter input ip saddr 127.0.0.1 ip daddr <other_ip> log prefix "Localhost to Other IP: " group 1
sudo nft add rule inet filter output ip saddr <other_ip> ip daddr 127.0.0.1 log prefix "Other IP to Localhost: " group 1


sudo nft add rule inet filter input ct state established log prefix "Established connection: " group 1
sudo nft add rule inet filter output ct state established log prefix "Established connection: " group 1
```

Filtering and analyzing network logs using nftables can be quite powerful, but it comes with its own set of challenges. Here are some common ones:

### Common Challenges

1. **Volume of Logs**:
   - **Challenge**: High traffic environments can generate a large volume of logs, making it difficult to manage and analyze.
   - **Solution**: Implement log rotation and use log management tools like `ulogd`, `syslog`, or centralized logging solutions like ELK Stack (Elasticsearch, Logstash, Kibana).

2. **Performance Impact**:
   - **Challenge**: Extensive logging can impact system performance, especially on high-traffic servers.
   - **Solution**: Log only essential information and use rate limiting to reduce the volume of logs.

3. **Log Noise**:
   - **Challenge**: Logs can contain a lot of irrelevant information, making it hard to find useful data.
   - **Solution**: Use specific logging rules to capture only relevant events and apply filters to exclude unnecessary information.

4. **Complexity of Rules**:
   - **Challenge**: Creating and maintaining complex logging rules can be error-prone and difficult to manage.
   - **Solution**: Keep rules simple and well-documented. Use comments and modularize rulesets by splitting them into multiple files.

5. **Correlation of Events**:
   - **Challenge**: Correlating events across different logs and systems can be challenging.
   - **Solution**: Use log aggregation and correlation tools that can combine logs from multiple sources and provide a unified view.

6. **Security and Privacy**:
   - **Challenge**: Logs can contain sensitive information that needs to be protected.
   - **Solution**: Implement access controls, encrypt log files, and ensure compliance with data protection regulations.

7. **Real-Time Analysis**:
   - **Challenge**: Analyzing logs in real-time to detect and respond to incidents can be difficult.
   - **Solution**: Use real-time monitoring tools and set up alerts for critical events.

8. **Storage Management**:
   - **Challenge**: Storing large volumes of logs can consume significant disk space.
   - **Solution**: Implement log rotation, compression, and archiving strategies to manage storage efficiently.

9. **Interpreting Logs**:
   - **Challenge**: Understanding and interpreting log entries can be complex, especially for non-standard protocols or custom applications.
   - **Solution**: Use log analysis tools that provide visualization and parsing capabilities to make logs easier to understand.

#### Best Practices

- **Regularly Review and Update Rules**: Ensure your logging rules are up-to-date with your security requirements.
- **Use Descriptive Prefixes**: Add meaningful prefixes to log entries to make them easier to identify.
- **Integrate with Analysis Tools**: Use tools like `ulogd` with databases or log analysis software for deeper insights.
- **Automate Log Management**: Use scripts and tools to automate log rotation, compression, and archiving.

By being aware of these challenges and implementing best practices, you can effectively filter and analyze network logs using nftables.

```sh


```

## References

- [iptables: The two variants and their relationship with nftables](https://developers.redhat.com/blog/2020/08/18/iptables-the-two-variants-and-their-relationship-with-nftables#)
- [nftables | The Main Commands](https://std.rocks/gnulinux_nftables_commands.html) 
- [nftables Wiki](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page)
- [nftables ArchLinux](https://wiki.archlinux.org/title/Nftables)
- [nft man](https://man.archlinux.org/man/nft.8)

How do I set up port forwarding using nftables?
What additional rules are needed for a web server or database server exposed to the internet?
Can you explain how to configure logging and monitoring in nftables?

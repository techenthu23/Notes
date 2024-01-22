---
layout: default
title:  "Red Hat Subscription Management"
---

- [Red Hat Subscription Management (RHSM)](#red-hat-subscription-management-rhsm)
  - [RHSM Features](#rhsm-features)
  - [Red Hat Labs Registration Assistant](#red-hat-labs-registration-assistant)
    - [Register and automatically subscribe in one step](#register-and-automatically-subscribe-in-one-step)
    - [Register first, then attach a subscription in the Customer Portal](#register-first-then-attach-a-subscription-in-the-customer-portal)
    - [Attach a specific subscription through the Customer Portal](#attach-a-specific-subscription-through-the-customer-portal)
    - [Attach a subscription from any available that match the system](#attach-a-subscription-from-any-available-that-match-the-system)
    - [Register with a specific pool](#register-with-a-specific-pool)
    - [Registration via GUI](#registration-via-gui)
    - [Connecting through a HTTP Proxy or Firewall](#connecting-through-a-http-proxy-or-firewall)
    - [Offline Registration](#offline-registration)
    - [Unregistering a system](#unregistering-a-system)
    - [Migrating from RHN Classic to RHSM](#migrating-from-rhn-classic-to-rhsm)
  - [How to register and subscribe a system offline to the Red Hat Customer Portal?](#how-to-register-and-subscribe-a-system-offline-to-the-red-hat-customer-portal)
    - [Note](#note)
- [Red Hat Network (RHN) Classic or RHM Satellite](#red-hat-network-rhn-classic-or-rhm-satellite)
  - [How to determine if system is using RHN Classic or RHSM for updates](#how-to-determine-if-system-is-using-rhn-classic-or-rhsm-for-updates)

# Red Hat Subscription Management (RHSM)

Red Hat Subscription Management (RHSM) is the service which manages your Red Hat subscriptions and entitlements. Red Hat Subscription Management allows users to track their subscription quantity and consumption. You can access your content through RHSM using Red Hat Satellite, Subscription Asset Manager (SAM), and the award-winning Customer Portal.

RHSM replaces Red Hat Network (RHN) Hosted. Red Hat decommissioned RHN in July 2017. You can no longer access or utilize APIs through the RHN interface or update your system using RHN.

RHSM uses three server-side tools:

- Open-source subscription service (Candlepin)
- Content service (CDN)
- Subscription service interfaces:  Customer Portal or Satellite 6

RHSM uses a client/server architecture and relies on the candlepin service for managing subscriptions. Red Hat hosts its instances of candlepin, so that they are always available. You can also deploy instances of candlepin through RHSM, Satellite 6, or SAM. Through this service, you can manage multiple systems and view all of the subscriptions attached to your account.

## RHSM Features

RHSM leverages the full value of your Red Hat subscriptions, entitlements, and contracts, as well as integrates with Red Hat’s system management tools to enable flexibility in your subscription choices.

For a full list of features supported by RHSM,, [click here](https://access.redhat.com/articles/63269).

RHSM helps your organization achieve four primary goals:

- Improving how subscriptions are applied
- Maintains dual inventories of available subscriptions and registered Red Hat systems
- System-to-subscription mapping makes it easier for you to apply appropriate subscriptions to systems
- Lowering costs and streamlining procurement
- Maintaining regulatory compliance by tracking subscription assignments and contract expiration
- Simplifying IT audit-readiness by offering a central inventory of current subscriptions and systems

Through RHSM, you can:

- Register systems using activation keys
- View a summary of contracts and the systems and orders applied to those contracts
- View subscription and entitlement information in one place in the user interface
- View a summary of subscription utilization, making the renewal decision easier
- View system compliance with RHSM on a system-by-system basis
- Transfer subscriptions

**NOTE: With Red Hat Subscription-Manager, registration and utilization of a subscription is actually a two-part process.**
***First register a system, then apply a subscription.***

## Red Hat Labs Registration Assistant

We have an online tool to assist you in selecting the most appropriate registration technology for your system. If you would prefer to use this tool, please visit <https://access.redhat.com/labs/registrationassistant/>.

### Register and automatically subscribe in one step

Use the following command to register the system, then automatically associate any available subscription matching that system:

```bash
subscription-manager register --username <username> --password <password> --auto-attach
```

If the command is unable to attach a subscription, it will indicate that in the output. Then, you can attach the subscription from the Customer Portal, instead (see the next section).

### Register first, then attach a subscription in the Customer Portal

Use the following command to register a system without immediately attaching a subscription:

```bash
subscription-manager register
```

### Attach a specific subscription through the Customer Portal

After registration, you can assign a subscription to the registered system from the Customer Portal by referring [this article](https://access.redhat.com/site/solutions/776723).

- After this, refresh the information on your machine using the following command. Be sure to run this any time you add or change the attached subscription from the Customer Portal:

```bash
subscription-manager refresh
```

### Attach a subscription from any available that match the system

After registration, use the following command to attach any available subscription that matches the current system.[Raw](https://access.redhat.com/solutions/253273#)

```bash
subscription-manager attach --auto
```

### Register with a specific pool

After registration, use the following command to attach a subscription from a specific pool:

```bash
subscription-manager attach --pool=<POOL_ID>
```

(You can find which pools are available with `subscription-manager list --available`)

**Note:** With subscription-manager-1.1.9-1 or later, **attach** option has been replacing the `subscribe` option. For more information, please refer to following article: [RHBA-2013-0350](https://rhn.redhat.com/errata/RHBA-2013-0350.html)

If you are not sure of the pool ID needed, these and details such as expiration dates can be viewed using the following command:

```bash
subscription-manager list --available --all
```

Using Virtual Data Center? You may find this solution helpful: [How to subscribe a VMware or Hyper-V guest using “Red Hat Enterprise Linux for Virtual Datacenter subscription”](https://access.redhat.com/solutions/659563)

### Registration via GUI

```bash
subscription-manager-gui
```

- Systems can also be registered with Customer Portal Subscription Management during the firstboot process or as part of the kickstart setup (both described in the [Installation Guide](<http://docs.redhat.co> m/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/sn-firstboot-updates.html))

### Connecting through a HTTP Proxy or Firewall

- [How to access Red Hat Subscription Manager (RHSM)through a firewall or proxy](https://access.redhat.com/site/solutions/65300)

### Offline Registration

Some systems may not have internet connectivity, but administrators still want to attach and track the subscriptions for that system. This can be done by [manually registering the system](https://access.redhat.com/solutions/3121571) using the Customer Portal.

### Unregistering a system

```bash
subscription-manager remove --all
subscription-manager unregister
subscription-manager clean
```

Also see [How to delete System Profiles for those registered with Red Hat Subscription Manager (RHSM)?](https://access.redhat.com/solutions/228883)

### Migrating from RHN Classic to RHSM

- [How to migrate a Red Hat Enterprise Linux System from RHN Classic to RHSM](https://access.redhat.com/solutions/129723)
- [Subscription-manager for the former Red Hat Network User](https://access.redhat.com/blogs/1169563/posts/2153731) – a 7-part series
- Additional instructions on registering, unregistering, and reregistering a system using Red Hat Subscription Management can be found in documentation
  - RHEL 6: [Red     Hat Enterprise Linux Deployment Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/chap-Subscription_and_Support-Registering_a_System_and_Managing_Subscriptions.html)
  - RHEL 7: [System     Administrator’s Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/chap-Subscription_and_Support-Registering_a_System_and_Managing_Subscriptions.html)
  - Subscription Management: [Red Hat     Subscription Management documentation](https://access.redhat.com/site/documentation/en-US/Red_Hat_Subscription_Management/).

- Including [Using         and Configuring Red Hat Subscription Manager](https://access.redhat.com/documentation/en-US/Red_Hat_Subscription_Management/1/html/RHSM/index.html)
        - [Subscription-manager for the former     Red Hat Network User](https://access.redhat.com/blogs/1169563/posts/2153731) – a 7-part series

## How to register and subscribe a system offline to the Red Hat Customer Portal?

To register an “offline” or “air-gapped” system, you need to manually create a system profile using [Red Hat Subscription Management (RHSM)](https://access.redhat.com/management/) in the Customer Portal. This profile serves as a placeholder and will not be connected to your actual system.

- **Create a system profile**
  
  From the [Systems](https://access.redhat.com/management/systems) page in RHSM, click the [New](https://access.redhat.com/management/systems/create) button. Provide the required information to finish creating the new system profile.

- **Attach subscriptions**
  
  In your newly created system profile, click the **Subscriptions** tab, and attach any subscriptions you want to use with the system.

- **Download and import the entitlement certificate(s)**
  
  From the “Subscriptions” tab on your system profile, click **Download Certificates** to download the entitlement certificate(s) for attached subscriptions. The downloaded archive will be in zip format and will be named similar to ‘aaaa1111-bb22-cc33-dd44-eeeeee555555\_certificates.zip’.

  ```bash
  # unzip -l d01bcb4f-8d59-433f-xxxx-0612dd2266db_certificates.zip 
    Archive:  d01bcb4f-8d59-433f-xxxx-0612dd2266db_certificates.zip
    signed Candlepin export for d01bcb4f-8d59-433f-xxxx-0612dd2266db
    Length      Date    Time    Name
    ---------  ---------- -----   ----
      18091  05-07-2018 14:35   consumer_export.zip
        512  05-07-2018 14:35   signature
    ---------           -------
      18603           2 files
  # unzip  d01bcb4f-8d59-433f-xxxx-0612dd2266db_certificates.zip
  ```

  This archive will contain another archive named ‘consumer\_export.zip’. Extract the content and in the `./export/entitlement_certificates/` folder you will find your certificate(s) in PEM format.

  ```bash
  # unzip -l consumer_export.zip 
    Archive:  consumer_export.zip
    Candlepin export for d01bcb4f-8d59-433f-xxxx-0612dd2266db
    Length      Date    Time    Name
    ---------  ---------- -----   ----
      23619  05-07-2018 14:35   export/entitlement_certificates/xxxx.pem
        137  05-07-2018 14:35   export/meta.json
    ---------           -------
      23756           2 files
  # unzip  consumer_export.zip
  ```

  Move these to the client system’s `/tmp` directory.

  ```bash
  subscription-manager import --certificate=/tmp /Name_Of_Downloaded_Entitlement_Cert.pem 
  subscription-manager import --certificate=/tmp/xxxx.pem
  ```

  This completes the registration of the offline system.

  You can verify that entitlement certificates were successfully imported by reviewing: `subscription-manager list --consumed`

### Note

When you register an online system via `subscription-manager register`, it automatically creates a connected profile on the Customer Portal, whereas in offline registration, you are manually creating a disconnected profile on the Portal.

After following this procedure, your system profile in the Customer Portal will show a subscription status “Unknown” and the command `subscription-manager status` will output “Unknown.” This is the expected behavior. For more information, see <https://access.redhat.com/solutions/2158251>

<https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux>#

# Red Hat Network (RHN) Classic or RHM Satellite

Red Hat Network (abbreviated to RHN) is a family of systems-management services operated by Red Hat. RHN makes updates, patches, and bug fixes of packages included within Red Hat Linux and Red Hat Enterprise Linux available to subscribers. Other available features include the deployment of custom content to, and the provisioning, configuration, reporting, monitoring of client systems.

## How to determine if system is using RHN Classic or RHSM for updates

__Environment__
- Red Hat Enterprise Linux 6
- Red Hat Enterprise Linux 5 ( 5.7 and higher)

The easiest way to discern what method of contacting Red Hat your system is using, is to check the output from a yum command such as yum repolist to review what plugins are in use:

```
$ clear
$ yum repolist
Loaded plugins: product-id, pulp-profile-update, refresh-packagekit, security, subscription-manager
This system is receiving updates from Red Hat Subscription Management.
rhel-6-server-cf-tools-1-rpms   | 2.8 kB     00:00
rhel-6-server-rhev-agent-rpms   | 3.1 kB     00:00
rhel-6-server-rpms              | 3.7 kB     00:00
repo id                            repo name                                                          status
rhel-6-server-cf-tools-1-rpms      Red Hat CloudForms Tools for RHEL 6 (RPMs)                             31
rhel-6-server-rhev-agent-rpms      Red Hat Enterprise Virtualization Agents for RHEL 6 Server (RPMs)      28
rhel-6-server-rpms                 Red Hat Enterprise Linux 6 Server (RPMs)                           10,872
repolist: 10,931
```

In the above output, we can see that the subscription-manager plugin is enabled, which helps in the process of handling interactions between the system, and the Red Hat Subscription Management server (cdn.redhat.com):

This system is receiving updates from Red Hat Subscription Management. Similarly, on a system using RHN Classic:

```
$ yum repolist
Loaded plugins: rhnplugin, security
This system is receiving updates from RHN Classic or RHN Satellite.
redhat-rhn-satellite-5.5-server-x86_64-6                     | 1.6 kB     00:00     
rhel-x86_64-server-6                                         | 1.8 kB     00:00     
rhn-tools-rhel-x86_64-server-6                               | 1.6 kB     00:00     
repo id                                     repo name                                                         status
redhat-rhn-satellite-5.5-server-x86_64-6    Red Hat Network Satellite (v5.5 for Server v6 AMD64 / Intel64)       354
rhel-x86_64-server-6                        Red Hat Enterprise Linux Server (v. 6 for 64-bit x86_64)          10,872
rhn-tools-rhel-x86_64-server-6              RHN Tools for RHEL (v. 6 for 64-bit x86_64)                          101
repolist: 11,327
```

We can see that the rhnplugin is loaded, with a clear notice that Classic is in use.

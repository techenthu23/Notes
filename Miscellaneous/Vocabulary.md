- [Scaffolding, scaffold](#scaffolding-scaffold)
- [Scrum](#scrum)
- [Deployment Strategies](#deployment-strategies)
  - [The Basic Deployment](#the-basic-deployment)
  - [Multi-Service Deployment](#multi-service-deployment)
  - [Rolling Depoyment](#rolling-depoyment)
  - [Blue—Green Deployment](#bluegreen-deployment)
  - [Canary Deployment](#canary-deployment)
  - [A/B resting](#ab-resting)
- [IDE](#ide)
- [Python Virtual Environment](#python-virtual-environment)
- [Cookie](#cookie)
  - [Different types of cookies - Magic Cookies and HTTP Cookies](#different-types-of-cookies---magic-cookies-and-http-cookies)
- [Security Standards](#security-standards)
- [REPL](#repl)
- [XDG](#xdg)

# Scaffolding, scaffold

Scaffolding, also called scaffold or staging, is a temporary structure used to support a work crew and materials to aid in the construction, maintenance and repair of buildings, bridges and all other man-made structures.

In education, scaffolding refers to a variety of instructional techniques used to move students progressively toward stronger understanding and, ultimately, greater independence in the learning process

# Scrum

  Scrum is used to divide a sophisticated software and product development task into smaller chunks using iterations and increasing practices. Scrum consist of three roles, such as:

- Product owner
- Scrum master
- Team

# Deployment Strategies

 Deployment strategies are practices used to change or upgrade a running instance of an application.

## The Basic Deployment

In a basic  deployment, all nodes within a target environment are updated at the same time with a new service or artifact version. Because of this, basic  deployments are not outage-proof and they slow down rollback processes or strategies. Of all the deployment strategies shared, it is the riskiest.

Pros :
The benefits of this strategy are that it is fast, and cheap. Use this strategy if
I) your application service is not business, mission, or revenue—critical, or
2) your deployment is to a lower environment, during off—hours, or with a service that is not in use.

Cons :

Of all the deployment strategies shared,   it is the riskiest and does not fall into best practices .  Basic deployments are not outage—proof and do not provide for easy rollbacks.

## Multi-Service Deployment

 In a multi—service deployment, all nodes within a target environment are updated with multiple new services simultaneously. This strategy is used for application services that have service or version dependencies, or if you're deploying off—hours to resources that are not in use.

Pros:

- Multi—service depioymen€S are fast, cheap, and not as risk—prone as a basic deployrent.

Cons:

- are alow to roll back and not outage-proof

Using this deployment Strategy also to difficulty in managing, testing,  and verifying all the service

## Rolling Depoyment

It is a deployment strategy that updates running instances Of an application with the new release.  All nodes in a target are increæntally with the service or artifact version in integer N batches.

Pros:

- It is relatively simple to rollback, less  risky than a basic deployænt, and the implementation is simple

Cons:

- Since nodes are updated in batches, it require services to support both new and old versions of an artifacts. Verification of an application deployment at every incremental change also makes this deployment slow.

## Blue—Green Deployment

Blue—green deployment is a deployment strategy that utilizes two identical environments, a "blue" (aka staging) and a "green" (aka prcduction) environrent with different versions of an application or service. Quality assurance and user acceptance testing are typically done within the blue environment that hosts new versions or changes. User traffic is shifted frcm the green environænt to the blue environment once new changes have been testing and accepted within the blue environment. You can then switch to the new environment once the deployment is successful.

Pros:

- One of the benefits of the blue—green deployment is that it is fast, well—understood, and easy to irpimplement . Rollback is also straightforward, because you can flip traffic back to the old environment in case of any issues. Blue—green deployments are therefore not as risky compared to other deployment strategies.

Cons :

- Cost is a drawback to blue—green deployments. Replicating a production environment can complex and expensive, especially when working with microservices.
- Quality assurance and acceptance testing may not identify all of the anomalies or regressions either, and so shifting all traffic at once can present risks
- An outage or issue could also have a wide—scale business impact before a rollback is triggered, and depending on the implementation, in—flight user transactions  may be lost when the shift in traffic is made.

## Canary Deployment

 A canary deployment is a deployment strategy that releases an application or service incrementally to a subset of users. All infrastructure in a target
 environment is updated in small phases ( eg 2% 25% 75%  100%) , A canary release is the lowest risk—prone, compared to all other deployment strategies,
 because of this control.

 Pros :

- Canary deployments allow organizations to test in production with real users and use cases and compare different service versions by side by side.
- Its cheaper than blue—green deployment because it does not require two environments.
- And finally, it is fast and safe to trigger a rollback to a previous version of an application.

Cons :

Drawbacks to canary deployments involve testing in production and the implementations needed. scripting a canary release can be complex: manual verification or testing can take time, and the required monitoring and instrumentation for testing in production may involve additional research.

## A/B resting

 In A/B testing, different versions of the same service run simultaneously as in the same environment for a period of time. Experiments are either controlled by feature flags toggling, A/B testing tools, or through distinct service deployments. It is the experiment owner's responsibility to define how user traffic is routed to each and version of an application. commonly, user traffic is routed based on specific rules or user demographics to perform measurements and comparisons between service versions. Target environments can then be updated with the optimal service version.

 The biggest difference between A/B testing and other deployment strategies is that A/B testing is primarily focused on experimentation and exploration. While other deployment strategies deploy many versions of a service to an environment with the in-mediate goal of updating all nodes with a specific version, A/B testing is about testing multiple ideas vs. deploying one specific tested idea.

 Pros :
 A/B testing is a standard, easy, and cheap method for testing new features in production. And luckily, there are many tools that exist today to help enable A/B testing.

 Cons :
 The drawbacks to A/ B testing involve the experimental nature of its use case. Experiments and tests can sometimes break the application, service, or user experience. Finally, scripting or automating AB tests can also be complex.

# IDE

 An integrated development environment (IDE) is software for building applications that combines common developer tools into a single
 graphical user interface (GUI) . An IDE typically consists of:
 — source code editor and support for multiple languages
 — Local build automation
 — Debugger
 — Plugins and extensions

# Python Virtual Environment

A virtual environment is a private copy of the Python interpreter onto which you can install packages privately, without affecting the global Python interpreter installed in your system. They are very useful because they prevent package clutter and version conflicts in the system’s Python interpreter. Creating a virtual environment for each application ensures that applications have access to only the packages that they use, while the global interpreter remains neat and clean and serves only as a source from which more virtual environments can be created.

# Cookie

HTTP cookies (also called web cookies, Internet cookies, browser cookies, or simply cookies) are small blocks/pieces of data created by a web server while a user is browsing a website and placed on the user's computer or other device by the user’s web browser. Cookies are placed on the device used to access a website, and more than one cookie may be placed on a user’s device during a session.

Data stored in a cookie is created by the server upon your connection. This data is labeled with an ID unique to you and your computer.

When the cookie is exchanged between your computer and the network server, the server reads the ID and knows what information to specifically serve to you.

## Different types of cookies - Magic Cookies and HTTP Cookies

- Magic Cookies
- HTTP Cookies

Cookies generally function the same but have been applied to different use cases:

"Magic cookies" are an old computing term that refers to packets of information that are sent and received without changes. Commonly, this would be used for a login to computer database systems, such as a business internal network. This concept predates the modern “cookie” we use today.

HTTP cookies are a repurposed version of the “magic cookie” built for internet browsing. Web browser programmer Lou Montulli used the “magic cookie” as inspiration in 1994. He recreated this concept for browsers when he helped an online shopping store fix their overloaded servers.

The HTTP cookie is what we currently use to manage our online experiences. It is also what some malicious people can use to spy on your online activity and steal your personal info.

# Security Standards

IT practitioners and managers in the US government are challenged with delivering high-quality applications quickly while maintaining compliance with a plethora of federal standards and certifications such as FIPS 140-2, FISMA, FedRAMP, and more- Public Sector Government departments, as well as many private companies who do business with them, must comply with the Security Technical Implementation Guide (STIG) published by the Defense Information Systems Agency (DISA).
Compliance to these standards greatly slows their ability to deliver infrastructure and applications and align their current environments to a government approved "Authorized to Operate" (ATO) state. To deliver value at velocity, integration of compliance into their development and operational processes accelerates those deliverables and aligns their specific production environments to the desired state.

# REPL

REPL stands for `Read-Eval-Print Loop`. It's a simple yet powerful interactive computer programming environment that takes single user inputs (expressions), evaluates them, and returns the results to the user. Key Characteristices: Interactivity, Immutability, Immediate feedback, Exploration and learning, Debugging and troubleshooting

# XDG

freedesktop.org (fd.o), formerly ***X Desktop Group*** (XDG), is a project to work on interoperability and shared base technology for free-software desktop environments for the X Window System (X11) and Wayland on Linux and other Unix-like operating systems. Although freedesktop.org produces specifications for interoperability, it is not a formal standards body.




**Shadow AI** refers to artificial intelligence systems operating beyond the visibility or control of those responsible for overseeing them. This can occur when AI applications are developed or used without being officially sanctioned or monitored by an organization’s IT department². Essentially, it's the unauthorized use of AI within an organization, which poses risks such as data breaches, compliance issues, and unreliable outputs⁵. If you're dealing with shadow AI, here are some steps you can take to address it:

1. **Decide Access Policies**: Determine how employees can access public generative AI tools. Consider written guidelines, firewalls, or VPNs to control access. Weigh the benefits of productivity and innovation against security risks¹.

2. **Communicate Policies**: Clearly communicate your organization's generative AI policies to employees. Let them know which tools they can use, for what purposes, and with what types of data¹.

Remember, managing shadow AI is crucial for maintaining security and data integrity within your organization. 😊

Source: Conversation with Copilot, 21/6/2024
(1) What is shadow AI & how to turn a liability into an asset. https://bing.com/search?q=AI+Shadow+Monitoring+-+What+is+it.
(2) How AI Can Help in Detecting and Managing Shadow Data. https://www.aidatafinder.com/ai-help-detecting-managing-shadow/.
(3) What Is Shadow AI And What Can IT Do About It? - Forbes. https://www.forbes.com/sites/delltechnologies/2023/10/31/what-is-shadow-ai-and-what-can-it-do-about-it/.
(4) What is shadow AI & how to turn a liability into an asset. https://www.walkme.com/blog/shadow-ai/.
(5) What Is Shadow AI? Enterprise IT's Latest Security Threat - Tech.co. https://tech.co/news/what-is-shadow-ai.


In the context of antivirus and cybersecurity, ASR stands for **Attack Surface Reduction**. It refers to a set of rules and features designed to minimize the areas through which an attacker can gain access to a system or network. ASR rules are part of Microsoft Defender for Endpoint and help prevent actions that malware often abuses to compromise devices and networks. These rules can block or audit behaviors like the execution of potentially obfuscated scripts, the creation of child processes by certain applications, and access to credential-stealing attacks on the Windows local security authority subsystem (lsass.exe), among others¹.

ASR is a proactive security measure that complements antivirus solutions by reducing the attackable surface area of a system, making it harder for threats to exploit vulnerabilities. It's an important aspect of a robust security posture, especially in enterprise environments.🛡️

Source: Conversation with Copilot, 11/6/2024
(1) Attack surface reduction rules reference - Microsoft Defender for .... https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction-rules-reference.
(2) Enable attack surface reduction rules - Microsoft Defender for Endpoint. https://learn.microsoft.com/en-us/defender-endpoint/enable-attack-surface-reduction.
(3) Demystifying attack surface reduction rules - Part 1. https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/demystifying-attack-surface-reduction-rules-part-1/ba-p/1306420.
(4) How to use Windows Defender Attack Surface Reduction rules. https://www.csoonline.com/article/570103/how-to-use-windows-defender-attack-surface-reduction-rules.html.
(5) Microsoft Defender Attack Surface Reduction Recommendations - Medium. https://blog.palantir.com/microsoft-defender-attack-surface-reduction-recommendations-a5c7d41c3cf8.


SCCM stands for System Center Configuration Manager. It's a systems management software developed by Microsoft that allows IT professionals to manage large groups of computers. SCCM provides capabilities such as remote control, patch management, software distribution, operating system deployment, and hardware and software inventory. It supports the management of Microsoft Windows and Windows Embedded operating systems and integrates with various Microsoft technologies and solutions to help increase IT productivity and efficiency, empower user productivity, and maintain compliance settings¹²³. If you're looking to manage and deploy applications and devices within an organization, SCCM can be a valuable tool. 🖥️🔧

(1) What is Configuration Manager? - Configuration Manager. https://learn.microsoft.com/en-us/mem/configmgr/core/understand/introduction.
(2) Microsoft Configuration Manager - Wikipedia. https://en.wikipedia.org/wiki/Microsoft_Configuration_Manager.
(3) Definition of SCCM | PCMag. https://www.pcmag.com/encyclopedia/term/sccm.
(4) What is the System Center Configuration Manager (SCCM)? - Definition .... https://www.techopedia.com/definition/29529/system-center-configuration-manager-sccm.
(5) Guide to Microsoft System Center Configuration Manager (SCCM .... https://www.spiceworks.com/tech/networking/articles/system-center-configuration-manager/.


Certainly! SCCM (System Center Configuration Manager) and Intune are both Microsoft products used for managing and deploying devices and applications within an organization, but they have different approaches and use cases.

**SCCM**:
- Is part of Microsoft Endpoint Manager.
- Is traditionally used for on-premises network environments.
- Uses an agent-based approach for management, giving administrators deep access to managed devices.
- Is ideal for complex, heterogeneous environments with a mix of servers, workstations, and laptops.
- Offers features like software distribution, patch management, operating system deployment, and hardware/software inventory.

**Intune**:
- Is a cloud-based mobile device management (MDM) and mobile application management (MAM) solution.
- Is suitable for managing a variety of devices, including mobile devices, laptops, and desktops.
- Uses a profile-based approach prescribed by the operating system manufacturer.
- Supports a remote and mobile workforce, allowing management from anywhere.
- Integrates with other Microsoft tools like Autopilot, Active Directory, and Office 365.

In summary, SCCM is more powerful and suitable for managing a diverse and complex IT environment, typically within larger organizations that have on-premises infrastructure. Intune, on the other hand, is more streamlined and cloud-oriented, ideal for organizations that need flexibility to manage devices remotely and support a mobile workforce¹².

(1) Intune vs SCCM: Comparison of Solutions | NinjaOne. https://www.ninjaone.com/blog/intune-vs-sccm-comparison-microsoft/.
(2) Microsoft Endpoint Configuration Manager (SCCM) vs Microsoft Intune .... https://blog.teamascend.com/sccm-vs-intune.
(3) SCCM vs Intune: Choose the Right Tool for the Task. https://bing.com/search?q=difference+between+SCCM+and+Intune.
(4) SCCM vs Intune: Choose the Right Tool for the Task. https://kollective.com/sccm-vs-intune-choose-the-right-tool-for-the-task/.
(5) Intune vs SCCM: Which one is better? - Communication Square LLC. https://www.communicationsquare.com/news/intune-vs-system-center-configuration-manager/.
(6) Microsoft Configuration Manager vs Microsoft Intune comparison - PeerSpot. https://www.peerspot.com/products/comparisons/microsoft-configuration-manager_vs_microsoft-intune.
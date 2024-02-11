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

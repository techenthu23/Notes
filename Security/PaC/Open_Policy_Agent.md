# Open Policy Agent (OPA)

- [Open Policy Agent (OPA)](#open-policy-agent-opa)
- [What is OPA?](#what-is-opa)
- [Why use OPA?](#why-use-opa)
- [Use Cases](#use-cases)
  - [OPA deployment](#opa-deployment)
  - [How Does OPA Works?](#how-does-opa-works)
  - [Deployment](#deployment)
  - [Data and Policies](#data-and-policies)
- [Rego](#rego)
- [References](#references)

# What is OPA?
The Open Policy Agent (OPA, pronounced “oh-pa”) is an open source, general-purpose policy engine that unifies policy enforcement across the stack. 

OPA is a policy engine that can be used to implement fine-grained access control for your application. 

For example, you can use OPA to implement authorization across microservices. However, there is much more that can be accomplished with OPA. For your inspiration, there are several open-source projects that integrate with OPA to implement fine-grained access control like Docker, Istio and others. Furthermore, OPA as a general-purpose policy engine, can be leveraged in use cases beyond access control, for instance to make advanced pod placement decisions in Kubernetes.

Open Policy Agent is an open-source engine that provides a way of declaratively writing policies as code and then using those policies as part of a decision-making process. It uses a policy language called Rego, allowing you to write policies for different services using the same language.

OPA provides a high-level declarative language that lets you specify policy as code and simple APIs to offload policy decision-making from your software. You can use OPA to enforce policies in microservices, Kubernetes, CI/CD pipelines, API gateways, and more.

OPA was originally created by [Styra](https://www.styra.com/) and is proud to be a graduated project in the Cloud Native [Computing Foundation (CNCF)](https://www.cncf.io/) landscape. For details read the CNCF announcement.

Services offload policy decisions to OPA by executing queries. OPA evaluates policies and data to produce query results (which are sent back to the client). Policies are written in a high-level declarative language and can be loaded into OPA via the filesystem or well-defined APIs.

OPA is written in the Go language and its source code is available on [GitHub](https://github.com/open-policy-agent/opa) under the Apache License 2.0. The Open Policy Agent project is hosted by [CNCF](https://www.cncf.io/) as an incubating project.

# Why use OPA?

- Without OPA, you need to  implement policy  management for your service from scratch. Required components must be carefully designed, implemented, and tested to ensure correct behaviour and a positive user experience, Tat's lot of work. OPA includes everything you need in order to policy enable any service.

- Stop using a different policy language, policy model, and policy API for every product and service you use. Use OPA for a unified toolset and framework for policy across the cloud native stack.

- Whether for one service or for all your services, use OPA to decouple policy from the service's code so you can release, analyze, and review policies (which security and compliance teams love) without sacrificing availability or performance.

- __Language:__ OPA uses the Rego language, which is a declarative language specifically designed for expressing policies. Rego is easy to read and write, making it accessible to both developers and operations teams.

- __Versatility:__ OPA is a versatile framework that can be integrated into various systems and cloud-native environments. It can be used for Kubernetes admission control, API authorization, data filtering, and more.

- __Declarative Nature:__ OPA allows you to declare policies in a declarative manner. Instead of specifying how to achieve a result, you declare what the result should be, which aligns well with the principles of Policy as Code.

- __Data-driven Policies:__ OPA allows you to write policies that consider complex data structures. This is beneficial for scenarios where policies depend on context or multiple sources of information.

- __Integrations:__ OPA integrates seamlessly with different tools and platforms. For example, it can be used with Kubernetes through Gatekeeper, with Docker for image scanning, with cloud providers for resource validation, and more.

- __Regularity Checking:__ OPA supports regularity checking, enabling you to express and enforce policies over hierarchical data structures.

- __Testability:__ OPA comes with built-in testing features, allowing you to write unit tests for your policies to ensure they behave as expected.

# Use Cases

OPA can be used for a number of purposes, including:

- Authorization of REST API endpoints.
- Allowing or denying Terraform changes based on compliance or safety rules.
- Integrating custom authorization logic into applications.
- Implementing Kubernetes Admission Controllers to validate API requests.

## OPA deployment


When it comes to deploying OPA, you have more than one option depending on your specific scenario:

- __As a Go library__: if your application is written in Golang, you can implement OPA as a third-party library in the application.
- __As a daemon__: if you’re not using Go, then you can deploy OPA just like any other service, as a daemon. 

  In this case, it’s recommended that you use a sidecar container or run it on the host level. The reason is that this design increases performance and availability. Imagine that you have OPA deployed in Kubernetes in a separate pod that happens to live on a separate node than where your application pod is running. Now, every time your service needs to consult OPA for a policy decision, it has to make a call over the network to reach the pod where OPA is running. This introduces unneeded latency and may cause application lags at peak times.


> Note that while you can offload authorization decisions from your application to OPA, your application still has to implement the enforcement of those decisions. For example, your application can ask OPA the question “Is user Alice allowed to invoke `GET /protected/resource?`” and if OPA answers “No”, your application has to send HTTP 403 Forbidden back to Alice


## How Does OPA Works?

OPA’s RESTful APIs use JSON over HTTP so you and your users can integrate OPA with any programming language. At a high level, integrating OPA into your service involves:

- Deploying OPA alongside your service
- Pushing relevant data about your service’s state into OPA’s document store
- Offloading some or all decision-making to OPA by querying it

When your service is integrated with OPA, your users will be able author and deploy custom policies that control the behavior of your service’s policy-enabled features. Furthermore, users can publish data to OPA that is not available to your service about their own deployment context.

1. __Define Policies:__ Write policies in Rego language. These policies express the desired state or conditions.

2. __Integrate with Systems:__ Embed OPA into your systems, applications, or workflows where policy enforcement is needed.

3. __Evaluate Policies:__ OPA evaluates policies against input data, making decisions based on the policy logic.

4. __Enforce Policies:__ If the evaluation results in a decision, OPA enforces the decision according to the policy's specifications.

5. __Policy Testing:__ Use OPA's testing capabilities to validate and ensure the correctness of your policies.


OPA decouples policy decision-making from policy enforcement. When your software needs to make policy decisions it queries OPA and supplies structured data (e.g., JSON) as input. OPA accepts arbitrary structured data as input.

![OPA Polciy Decoupling](../../Gallery/OPA_Policy_decoupling)

OPA generates policy decisions by evaluating the query input against policies and data. OPA and Rego are domain-agnostic so you can describe almost any kind of invariant in your policies. For example:

- Which users can access which resources.
- Which subnets egress traffic is allowed to.
- Which clusters a workload must be deployed to.
- Which registries binaries can be downloaded from.
- Which OS capabilities a container can execute with.
- Which times of day the system can be accessed at.

Policy decisions are not limited to simple yes/no or allow/deny answers. Like query inputs, your policies can generate arbitrary structured data as output.

## Deployment

Unless you embed OPA as a Go library, you will deploy it alongside your service – either directly as an operating system daemon or inside a container. In this way, transactions will have low latency and availability will be determined through shared fate with your service.

When OPA starts for the first time, it will not contain any policies or data. Policies and data can be added, removed, and modified at any time. For example: by deployment automation software or your service as it is deployed, by your service during an upgrade, or by administrators as needed.

## Data and Policies

The primary unit of data in OPA is a document, which is similar to a JSON value. Documents typically correspond to single, self-contained objects and are capable of representing both primitive types (strings, numbers, booleans, and null) as well as structured types (objects, and arrays). Documents are created, read, updated, and deleted via OPA’s RESTful HTTP APIs.

# Rego

OPA policies are expressed in a high-level declarative language called Rego. Rego (pronounced “ray-go”) is purpose-built for expressing policies over complex hierarchical data structures. 

For detailed information on Rego see the [Policy Language](https://www.openpolicyagent.org/docs/latest/policy-language) documentation.

---

# References

- [GETTING STARTED WITH ENVOY & OPEN POLICY AGENT — 04](https://helpfulbadger.github.io/blog/envoy_opa_4_opa_cli/
)

- [Open Policy Agent](https://www.openpolicyagent.org/docs/latest/)
- [Policy Language - Rego](https://www.openpolicyagent.org/docs/latest/policy-language)
- [Introducing Policy As Code: The OPA](https://www.cncf.io/blog/2020/08/13/introducing-policy-as-code-the-open-policy-agent-opa/)
# Open Policy Agent (OPA)

- [Open Policy Agent (OPA)](#open-policy-agent-opa)
- [What is OPA?](#what-is-opa)
- [Why use OPA?](#why-use-opa)
  - [How Does OPA Works?](#how-does-opa-works)
  - [Deployment](#deployment)
  - [Data and Policies](#data-and-policies)
- [References](#references)

# What is OPA?
The Open Policy Agent (OPA, pronounced “oh-pa”) is an open source, general-purpose policy engine that unifies policy enforcement across the stack. OPA provides a high-level declarative language that lets you specify policy as code and simple APIs to offload policy decision-making from your software. You can use OPA to enforce policies in microservices, Kubernetes, CI/CD pipelines, API gateways, and more.

OPA was originally created by [Styra](https://www.styra.com/) and is proud to be a graduated project in the Cloud Native [Computing Foundation (CNCF)](https://www.cncf.io/) landscape. For details read the CNCF announcement.

Services offload policy decisions to OPA by executing queries. OPA evaluates policies and data to produce query results (which are sent back to the client). Policies are written in a high-level declarative language and can be loaded into OPA via the filesystem or well-defined APIs.

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

## Deployment

Unless you embed OPA as a Go library, you will deploy it alongside your service – either directly as an operating system daemon or inside a container. In this way, transactions will have low latency and availability will be determined through shared fate with your service.

When OPA starts for the first time, it will not contain any policies or data. Policies and data can be added, removed, and modified at any time. For example: by deployment automation software or your service as it is deployed, by your service during an upgrade, or by administrators as needed.

## Data and Policies

The primary unit of data in OPA is a document, which is similar to a JSON value. Documents typically correspond to single, self-contained objects and are capable of representing both primitive types (strings, numbers, booleans, and null) as well as structured types (objects, and arrays). Documents are created, read, updated, and deleted via OPA’s RESTful HTTP APIs.


---

# References

- [GETTING STARTED WITH ENVOY & OPEN POLICY AGENT — 04](https://helpfulbadger.github.io/blog/envoy_opa_4_opa_cli/
)

- [Open Policy Agent](https://www.openpolicyagent.org/docs/latest/)
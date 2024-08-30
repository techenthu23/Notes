
- [Microservices](#microservices)
- [Introduction](#introduction)
- [How microservices work](#how-microservices-work)
- [Main components of Microservices Architecture](#main-components-of-microservices-architecture)
- [Design Patterns of Microservices](#design-patterns-of-microservices)
- [Anti-Patterns in Microservices](#anti-patterns-in-microservices)
- [Real-World Example of Microservices](#real-world-example-of-microservices)
  - [Amazon E-Commerce Application](#amazon-e-commerce-application)
- [Microservices vs. Monolithic Architecture](#microservices-vs-monolithic-architecture)
  - [How to move from Monolithic to Microservices?](#how-to-move-from-monolithic-to-microservices)
- [Service-Oriented Architecture(SOA) vs. Microservices Architecture](#service-oriented-architecturesoa-vs-microservices-architecture)
- [Cloud-native Microservices](#cloud-native-microservices)
- [Role of Microservices in DevOps](#role-of-microservices-in-devops)
- [Benefits of using Microservices Architecture](#benefits-of-using-microservices-architecture)
- [Challenges of using Microservices Architecture](#challenges-of-using-microservices-architecture)
- [Real-World Examples of Companies using Microservices Architecture](#real-world-examples-of-companies-using-microservices-architecture)
- [Technologies that enables microservices architecture](#technologies-that-enables-microservices-architecture)
- [Top 10 Microservices Frameworks](#top-10-microservices-frameworks)
  - [Spring boot and Spring Cloud (Java)](#spring-boot-and-spring-cloud-java)
  - [Express.js (Node.js)](#expressjs-nodejs)
  - [Django + Django REST Framework (Python)](#django--django-rest-framework-python)
  - [Microservices with Go kit in GoLang](#microservices-with-go-kit-in-golang)
  - [Micronaut (Java, Groovy, Kotlin)](#micronaut-java-groovy-kotlin)
  - [Eclipse MicroProfile (Java)](#eclipse-microprofile-java)
  - [FastAPI (Python)](#fastapi-python)
  - [Helidon Oracle’s Dual-Purpose Java Solution for Microservices](#helidon-oracles-dual-purpose-java-solution-for-microservices)
  - [Lagom (Scala and Java)](#lagom-scala-and-java)
  - [Quarkus (Java)](#quarkus-java)
- [Types of Microservices](#types-of-microservices)

# Microservices

# Introduction

**Microservices** are an architectural approach to developing software applications as a collection of small, loosely coupled distributed services that communicate with each other over a network. Instead of building a monolithic application where all the functionality is tightly integrated into a single codebase, microservices break down the application into smaller, loosely coupled services.

It allows you to take a large application and decompose or break it into easily manageable small components with narrowly defined responsibilities providing flexibility, scalability, and easier maintenance,

It is considered the building block of modern applications. Microservices can be written in a variety of programming languages, and frameworks, and each service acts as a mini-application on its own.

# How microservices work

1. **Modular Structure**:

- Microservices architecture breaks down large, monolithic applications into smaller, independent services.
- Each service is a self-contained module with a specific business capability or function.
- This modular structure promotes flexibility, ease of development, and simplified maintenance.

2. **Independent Functions**:

- Each microservice is designed to handle a specific business function or feature.
- For example, one service may manage user authentication, while another handles product catalog functions.
- This independence allows for specialized development and maintenance of each service.

3. **Communication**:

- Microservices communicate with each other through well-defined Application Programming Interfaces (APIs).
- APIs serve as the interfaces through which services exchange information and requests.
- This standardized communication enables interoperability and flexibility in integrating services.

4. **Flexibility**:

- Microservices architecture supports the use of diverse technologies for each service.
- This means that different programming languages, frameworks, and databases can be chosen based on the specific requirements of each microservice.
- Teams have the flexibility to use the best tools for their respective functions.

5. **Independence and Updates**:

- Microservices operate independently, allowing for updates or modifications to one service without affecting the entire system.
- This decoupling of services reduces the risk of system-wide disruptions during updates, making it easier to implement changes and improvements.
- Also Microservices contribute to system resilience by ensuring that if one service encounters issues or failures, it does not bring down the entire system.

6. **Scalability**:

- Microservices offer scalability by allowing the addition of instances of specific services.
- If a particular function requires more resources, additional instances of that microservice can be deployed to handle increased demand.
- This scalability is crucial for adapting to varying workloads.

6. **Continuous Improvement**:

- The modular nature of microservices facilitates continuous improvement.
- Development teams can independently work on and release updates for their respective services.
- This agility enables the system to evolve rapidly and respond to changing requirements or user needs.

# Main components of Microservices Architecture

Microservices architecture comprises several components that work together to create a modular, scalable, and independently deployable system.

The main components of microservices include:

1. **Microservices**: These are the individual, self-contained services that encapsulate specific business capabilities. Each microservice focuses on a distinct function or feature.

2. **API Gateway**: The API Gateway is a central entry point for external clients to interact with the microservices. It manages requests, handles authentication, and routes requests to the appropriate microservices.

3. **Service Registry and Discovery**: This component keeps track of the locations and network addresses of all microservices in the system. Service discovery ensures that services can locate and communicate with each other dynamically.

4. **Load Balancer**: Load balancers distribute incoming network traffic across multiple instances of microservices. This ensures that the workload is evenly distributed, optimizing resource utilization and preventing any single service from becoming a bottleneck.

5. **Containerization**: Containers, such as Docker, encapsulate microservices and their dependencies. Orchestration tools, like Kubernetes, manage the deployment, scaling, and operation of containers, ensuring efficient resource utilization.

6. **Event Bus/Message Broker**_: An event bus or message broker facilitates communication and coordination between microservices. It allows services to publish and subscribe to events, enabling asynchronous communication and decoupling.

7. **Centralized Logging and Monitoring**: Centralized logging and monitoring tools help track the performance and health of microservices. They provide insights into system behavior, detect issues, and aid in troubleshooting.

8. **Database per Microservice**: Each microservice typically has its own database, ensuring data autonomy. This allows services to independently manage and scale their data storage according to their specific requirements.

9. **Caching**: Caching mechanisms can be implemented to improve performance by storing frequently accessed data closer to the microservices. This reduces the need to repeatedly fetch the same data from databases.

10. **Fault Tolerance and Resilience Components**: Implementing components for fault tolerance, such as circuit breakers and retry mechanisms, ensures that the system can gracefully handle failures in microservices and recover without impacting overall functionality.

# Design Patterns of Microservices

When a problem occurs while working on a system, there are some practices that are to be followed and in microservices, those practices are Design Patterns.

Microservices design patterns are such practices which when followed lead to efficient architectural patterns resulting in overcoming challenges such as inefficient administration of these services and also maximizing performance. While working on an application, one must be aware of which design pattern to be used for creating an efficient application.

1. **Aggregator**

- It invoked services to receive the required information (related data) from different services, apply some logic and produce the result.
- The data collected can be utilized by the respective services. The steps followed in the aggregator pattern involve the request received by the service, and then the request made to multiple other services combines each result and finally responds to the initial request.

2. **API Gateway**

- API Gateway acts as a solution to the request made to microservices.
- It serves as an entry point to all the microservices and creates fine-grained APIs for different clients.
- Requests made are passed to the API Gateway and the load balancer helps in checking whether the request is handled and sent to the respective service.

3. **Event Sourcing**

- This design pattern creates events regarding changes (data) in the application state.
- Using these events, developers can keep track of records of changes made.

4. **Strangler**

- Strangler is also known as a Vine pattern since it functions the same way vine strangles a tree around it. For each URI (Uniform Resource Identifier) call, a call goes back and forth and is also broken down into different domains.
- Here, two separate applications remain side by side in the same URI space, and here one domain will be taken into account at a time. Thus, the new refactored application replaces the original application.

5. **Decomposition**

- Decomposition design pattern is decomposing an application into smaller microservices, that have their own functionality.
- Based on the business requirements, you can break an application into sub-components. For example, Amazon has separate services for products, orders, customers, payments, etc.

# Anti-Patterns in Microservices

Learning antipatterns in microservices is crucial for avoiding common mistakes. It provides insights into potential issues that can compromise system scalability, independence, and maintainability. By understanding these antipatterns, developers can make informed decisions, implement best practices, and contribute to the successful design and deployment of robust microservices architectures.

Below are the main 5 Antipatterns in microservices

1. **Data Monolith**: Sharing a centralized database among microservices, undermining independence and scalability.

2. **Chatty Services**: Microservices overly communicating for small tasks, leading to increased network overhead and latency.

3. **Overusing Microservices**: Creating too many microservices for trivial functionalities, introducing unnecessary complexity.

4. **Inadequate Service Boundaries**: Poorly defined boundaries of microservices, resulting in ambiguity and unclear responsibilities.

5. **Ignoring Security**: Neglecting security concerns in microservices, risking vulnerabilities and data breaches.

# Real-World Example of Microservices

## Amazon E-Commerce Application

Let’s understand the Miscroservices using the real-world example of Amazon E-Commerce Application:

Amazon’s online store is like a giant puzzle made of many small, specialized pieces called microservices. Each microservice does a specific job to make sure everything runs smoothly. Together, these microservices work behind the scenes to give you a great shopping experience.

Below are the microservices involved in Amazon E-commerce Application:

1. **User Service**: Manages user accounts, authentication, and preferences. It handles user registration, login, and profile management, ensuring a personalized experience for users.

2. **Search Service**: Powers the search functionality on the platform, enabling users to find products quickly. It indexes product information and provides relevant search results based on user queries.

3. **Catalog Service**: Manages the product catalog, including product details, categories, and relationships. It ensures that product information is accurate, up-to-date, and easily accessible to users.

4. **Cart Service**: Manages the user’s shopping cart, allowing them to add, remove, and modify items before checkout. It ensures a seamless shopping experience by keeping track of selected items.

5. **Wishlist Service**: Manages user wishlists, allowing them to save products for future purchase. It provides a convenient way for users to track and manage their desired items.

6. **Order Taking Service**: Accepts and processes orders placed by customers. It validates orders, checks for product availability, and initiates the order fulfillment process.

7. **Order Processing Service**: Manages the processing and fulfillment of orders. It coordinates with inventory, shipping, and payment services to ensure timely and accurate order delivery.

8. **Payment Service**: Handles payment processing for orders. It securely processes payment transactions, integrates with payment gateways, and manages payment-related data.

9. **Logistics Service**: Coordinates the logistics of order delivery. It calculates shipping costs, assigns carriers, tracks shipments, and manages delivery routes.

10. Warehouse Service: Manages inventory across warehouses. It tracks inventory levels, updates stock availability, and coordinates stock replenishment.

11. Notification Service: Sends notifications to users regarding their orders, promotions, and other relevant information. It keeps users informed about the status of their interactions with the platform.

12. Recommendation Service: Provides personalized product recommendations to users. It analyzes user behavior and preferences to suggest relevant products, improving the user experience and driving sales.

# Microservices vs. Monolithic Architecture

Below is a tabular comparison between microservices and monolithic architecture across various aspects:

| Aspect                     | Microservices Architecture                                      | Monolithic Architecture |
| :------------------------- | :-------------------------------------------------------------- | :---------------------- |
| Architecture Style         | Decomposed into small, independent services.                    | Single, tightly integrated codebase. |
| Development Team Structure | Small, cross-functional teams for each microservice.            | Larger, centralized development team. |
| Scalability                | Independent scaling of individual services.                     | Scaling involves replicating the entire application. |
| Deployment                 | Independent deployment of services.                             | Whole application is deployed as a single unit. |
| Resource Utilization       | Efficient use of resources as services can scale independently. | Resources allocated based on the overall application’s needs. |
| Development Speed          | Faster development and deployment cycles.                       | Slower development and deployment due to the entire codebase. |
| Flexibility                | Easier to adopt new technologies for specific services.         | Limited flexibility due to a common technology stack. |
| Maintenance                | Easier maintenance of smaller, focused codebases.               | Maintenance can be complex for a large, monolithic codebase. |
| Testing                    | Independent testing of each microservice.                       | Comprehensive testing of the entire application. |
| Infrastructure Dependency  | Less dependent on specific infrastructure choices.              | Tied to specific infrastructure due to a shared codebase. |

## How to move from Monolithic to Microservices?

Below are the main the key steps to move from a monolithic to microservices architecture:

1. **Evaluate Monolith****: Understand the existing monolithic application, identifying components for migration.
2. **Define Microservices: Break down the monolith into distinct business capabilities for microservices.
3. **Strangler Pattern**: Gradually replace monolithic parts with microservices, adopting a gradual migration approach.
4. **API Definition**: Clearly define APIs and contracts for seamless microservices communication.
5. **CI/CD Implementation**: Set up Continuous Integration/Continuous Deployment (CI/CD) for automated testing and deployment.
6. **Decentralize Data**: Transition to a database-per-service approach, reducing dependencies on a central database.
7. **Service Discovery**: Introduce service discovery mechanisms for dynamic communication between microservices.
8. **Logging and Monitoring**: Implement centralized logging and monitoring for visibility into microservices’ performance.
9. **Cross-Cutting Concerns**: Manage cross-cutting concerns like security and authentication consistently across microservices.
10. **Iterative Improvement**: Embrace an iterative approach, continuously refining and expanding microservices based on feedback and evolving needs.

# Service-Oriented Architecture(SOA) vs. Microservices Architecture

Below is a tabular comparison between Service-Oriented Architecture (SOA) and Microservices across various aspects:

| Aspect                | Service-Oriented Architecture(SOA)                                  | Microservices Architecture |
| :-------------------- | :------------------------------------------------------------------ | :------------------------- |
| Scope                 | Includes a broad set of architectural principles.                   | Focuses on building small, independent services. |
| Size of Services      | Services tend to be larger and more comprehensive.                  | Services are small, focused, and single-purpose. |
| Data Management       | Common data model and shared databases are common.                  | Each service has its own database or data store. |
| Communication         | Typically relies on standardized protocols like SOAP.               | Uses lightweight protocols such as REST or messaging. |
| Technology Diversity  | Can have different technologies, but often standardized middleware. | Encourages diverse technologies for each service. |
| Deployment            | Services are often deployed independently.                          | Promotes independent deployment of microservices. |
| Scalability           | Horizontal scaling of entire services is common.                    | Enables independent scaling of individual services. |
| Development Speed     | Slower development cycles due to larger services.                   | Faster development cycles with smaller services. |
| Flexibility           | Can be flexible, but changes may affect multiple services.          | Provides flexibility due to independent services. |
| Resource Utilization  | Resources may be underutilized during low demand.                   | Efficient use of resources, as services can scale independently. |
| Dependency Management | Relies on shared components and centralized governance.             | Each microservice manages its dependencies independently. |
| Adoption Difficulty   | Generally requires more planning and organizational change.         | Easier to adopt incrementally and suitable for agile development. |

# Cloud-native Microservices

Microservices and cloud each other by providing a flexible, efficient, and collaborative environment for building and running software applications

- Simplified Operations Cloud providers handle infrastructure maintenance and security, making it simpler for the microservices teams. They can focus on their specific tasks without worrying about the background technicalities.
- Cost-Efficiency Combining microservices with cloud resources is like paying for the exact tools and workspace you use. It’s cost-efficient because you’re not stuck with unnecessary equipment or space.
- Flexibility Need more teams or want to change your production process? The cloud allows you to adapt quickly, like rearranging workstations in a flexible workspace.

# Role of Microservices in DevOps

DevOps and microservices are closely aligned and often go hand in hand to enhance the development, deployment, and operational aspects of modern software systems. Here’s a brief overview of how DevOps and microservices work together:

1. **Continuous Integration/Continuous Deployment (CI/CD)**:

- In a microservices architecture, each service can be independently developed, tested, and deployed. CI/CD pipelines are crucial for efficiently managing the constant updates and releases associated with microservices.
- DevOps practices emphasize CI/CD pipelines, which involve automating the building, testing, and deployment of software.

2. **Agile Development**:

- Microservices inherently support agile development by allowing teams to work independently on specific services, facilitating rapid iteration and deployment of new features.
- DevOps promotes collaboration between development and operations teams, fostering agile development practices.

3. Continuous Monitoring and Logging

- Microservices architecture requires robust monitoring to track the health and interactions between various services, aiding in early issue detection and resolution. DevOps emphasizes continuous monitoring and logging for real-time insights into application performance.

# Benefits of using Microservices Architecture

1. **Modularity and Decoupling**:

- Independent Development: Microservices are developed and deployed independently, allowing different teams to work on different services simultaneously.
- Isolation of Failures: Failures in one microservice do not necessarily affect others, providing increased fault isolation.
  
2. **Scalability**:

- Granular Scaling: Each microservice can be scaled independently based on its specific resource needs, allowing for efficient resource utilization.
- Elasticity: Microservices architectures can easily adapt to varying workloads by dynamically scaling individual services.

3. **Technology Diversity**:

- Freedom of Technology: Each microservice can be implemented using the most appropriate technology stack for its specific requirements, fostering technological diversity.

4. **Autonomous Teams**:

- Team Empowerment: Microservices often enable small, cross-functional teams to work independently on specific services, promoting autonomy and faster decision-making.
- Reduced Coordination Overhead: Teams can release and update their services without requiring extensive coordination with other teams.

5. **Rapid Deployment and Continuous Delivery**:
    - Faster Release Cycles: Microservices can be developed, tested, and deployed independently, facilitating faster release cycles.
    - Continuous Integration and Deployment (CI/CD): Automation tools support continuous integration and deployment practices, enhancing development speed and reliability.

6. **Easy Maintenance**:
    - Isolated Codebases: Smaller, focused codebases are easier to understand, maintain, and troubleshoot.
    - Rolling Updates: Individual microservices can be updated or rolled back without affecting the entire application.

# Challenges of using Microservices Architecture

1. **Complexity of Distributed Systems**: Microservices introduce the complexity of distributed systems. Managing communication between services, handling network latency, and ensuring data consistency across services can be challenging.

2. **Increased Development and Operational Overhead**: The decomposition of an application into microservices requires additional effort in terms of development, testing, deployment, and monitoring. Teams need to manage a larger number of services, each with its own codebase, dependencies, and deployment process.

3. **Inter-Service Communication Overhead**: Microservices need to communicate with each other over the network. This can result in increased latency and additional complexity in managing communication protocols, error handling, and data transfer.

4. **Data Consistency and Transaction Management**: Maintaining data consistency across microservices can be challenging. Implementing distributed transactions and ensuring data integrity becomes complex, and traditional ACID transactions may not be easily achievable.

5. **Deployment Challenges**: Coordinating the deployment of multiple microservices, especially when there are dependencies between them, can be complex. Ensuring consistency and avoiding service downtime during updates require careful planning.

6. **Monitoring and Debugging Complexity**: Monitoring and debugging become more complex in a microservices environment. Identifying the root cause of issues may involve tracing requests across multiple services, and centralized logging becomes crucial for effective debugging.

# Real-World Examples of Companies using Microservices Architecture

Organizations experienced a massive change while using microservice in their application, and that’s where the transition from monolithic to microservice came. You can go through some of the real-life examples in applications that use microservice are:

- Amazon: Initially, Amazon was a monolithic application but when microservice came into existence, Amazon was the first platform to break its application into small components, thereby adapting microservice. Due to its ability to change individual features and resources, the site’s functionality improved to a massive extent.
- Netflix: Netflix is one such company that uses microservices with APIs. In 2007, when Netflix started its move towards movie-streaming service, it suffered huge service outages and challenges, then came the microservice architecture which was a blessing to the platform.
- Uber: When Uber switched from monolithic nature to a microservice, it experienced a smooth way. Using microservice architecture, the webpage views and searches increased to a greater extent.

# Technologies that enables microservices architecture

- Docker:
  - Docker is a containerization platform that allows developers to package applications and their dependencies into lightweight, portable containers. These containers encapsulate everything needed to run the application, including code, runtime, libraries, and system tools, ensuring consistency across different environments.
- Kubernetes:
  - Kubernetes is an open-source container orchestration platform originally developed by Google. It automates the deployment, scaling, and management of containerized applications, providing features for container scheduling, service discovery, load balancing, and more.
- Service Mesh:
  - Service mesh technologies like Istio and Linkerd provide a dedicated infrastructure layer for handling service-to-service communication, traffic management, and observability in microservices architectures. They offer features like load balancing, service discovery, circuit breaking, and metrics collection.
- API Gateways:
  - API gateways such as Kong and Tyk serve as entry points for external clients to access microservices-based applications. They provide functionalities like routing, authentication, rate limiting, and request/response transformations.
- Event-Driven Architecture:
  - Event-driven architectures facilitate communication between microservices by allowing them to produce and consume events asynchronously. Technologies like Apache Kafka, RabbitMQ, and Amazon SNS/SQS provide scalable, reliable messaging systems for building event-driven microservices.
- Serverless Computing:
  - While not exclusive to microservices, serverless platforms like AWS Lambda, Azure Functions, and Google Cloud Functions can be used to deploy individual microservices without managing the underlying infrastructure, further decoupling and scaling services.

# Top 10 Microservices Frameworks

When planning a business application or software development, the main task is to choose the tech stack that could make your application faster, secure, and scalable. So, when building an application, the foundation matters. Choosing the right framework is vital; it affects costs, development time, ease of management, and long-term maintenance.

microservices frameworks used by developers

## Spring boot and Spring Cloud (Java)

Spring Boot is like a toolkit for building apps using Java. It’s especially good for creating small parts of big systems, called microservices. With Spring Cloud, it gets even more tools to make these microservices work well together. Basically, Spring Boot is like a toolkit that makes creating modern Java apps much easier and faster by removing repetitive setup steps which are paired with Spring Cloud, which offers specialized tools for building tiny interconnected apps called ‘microservices’, things like finding services, setting configurations across multiple services.

It follows the Model-View-Controller (MVC) design pattern and comes with an ORM (Object-Relational Mapping), which allows the developers to interact with databases using Hibernate instead of complex SQL Queries.

Key Benefits:

 1. It has auto-configuration which automatically configures your application, based on the libraries you have in your project.
 2. It creates standalone spring applications that can be started using Java’s main method and just needs to put some annotation in the main spring boot application.
 3. Spring cloud has service discovery which allows the automatic detection of network locations for service instances, and makes it easier for services to find and communicate with each other.
 4. It manages the application configurations across multiple services.

## Express.js (Node.js)

Express.js is the web application framework for Node.js, it’s designed to help developers build various web applications and APIs easily as well as quickly. So, as a part of Node.js environment, it’s based on Javascript. It stands out as a top choice of developers, as it’s lightweight, fast, and is adaptable enough for microservices which are equipped with the such suitable plugins.

Key Benefits:

1. Express.js provides just the essentials to get a server up and running, making it fast and efficient.
2. By using express.js you can easily use middleware to add functionalities where middlewares are like plugins; they can handle tasks like parsing request data, managing cookies, or even handling authentication.
3. As it’s built on Node.js, it can handle many connections simultaneously without slowing down the server.
4. There are many plugins available for the tasks ranging from template engine integration to the security features.

## Django + Django REST Framework (Python)

Django is a high-level web framework for Python developers that promotes the rapid development of secure and maintainable websites. It follows the “batteries-included” philosophy, which means it comes pre-equipped with modules and such libraries for many common web development tasks. This includes functionalities like ORM (Object-Relational Mapping) for database interactions, in Django there are automatic admin interfaces, and built-in security features to prevent common web attacks.

DRF (Django REST Framework) which is an extension for Django that is built for creating the RESTful web APIs, as well as it offers the serializers to convert complex data types, like Django models, into the easily renderable formats like JSON. Additionally, it provides built-in tools for authentication, pagination, and view handling, also simplifying the process of API endpoint creation.

Key Benefits:

1. Both Django and DRF are designed for speed, by using them you can easily get a functional application or API up within a short time.
2. Django and DRF have strong community support, and their documentation is comprehensive and user-friendly.
3. Django includes a dynamic admin interface, ready to use without any additional coding, which is a boon for developers who need to quickly set up administrative functions.
4. It includes such plugins or packages for almost everything.

## Microservices with Go kit in GoLang

Go kit is a programming toolkit for building microservices, Go programming language is renowned for its emphasis on concurrent operations and performance, seamlessly aligns with the microservices paradigm. Basically, the GoLang is known for its robust concurrency model and efficiency, naturally blends with the demands of microservices.

It provides great guidance and solutions for most of the common challenges that are faced during the development of microservices, it aims to help developers in transition from a team of go developers to a team of go engineers. Go kit is designed in such a way that it doesn’t tie you down to a particular method of data transport, whether you use HTTP, gRPC, Thrift, or others, go kit can handle it.

One of go kit’s core concepts is the “endpoint” where each service method gets represented as an endpoint which makes it easy to define and handle the all over functionalities like rate limiting, circuit breaking, and request tracing on a per-method basis.

Key Benefits:
Go kit provides tools that help in scaling not just the application but also the team working on it.
With its transport approach and the availability of encoding/decoding utilities, go kit ensures that your microservices can interact seamlessly.
Go kit integrates tools for logging, metrics, and tracing out-of-the-box, allowing for better monitoring, observability, and debugging of microservices.
Go community provides plugins, and community support.

## Micronaut (Java, Groovy, Kotlin)

Micronaut is just like a rising star in the JVM (Java Virtual Machine) enviornment whether you code in Java, Groovy, or Kotlin, Micronauts ahead-of-time (AOT) compilation optimises your microservices applications, which also reduces the overhead and startup times.

Basically, it’s the modern framework which is designed to make server-side and serverless applications that are easy to write and efficient to run, it builds microservices and serverless functions. Micronaut is built on reactive libraries that can handle a large number of concurrent users efficiently.

Key Benefits:

1. Micronauts standout features is its AOT approach that is Ahead-of-Time (AOT) Compilation.
2. Due to AOT compilation, micronaut applications increase the fast startup times and reduce the memory usage.
3. Its reactive foundation ensures applications are scalable, and can manage a large number of concurrent requests.
4. Micronaut brings powerful and fast dependency injection features.

## Eclipse MicroProfile (Java)

The Eclipse MicroProfile came into the world when there was a need to optimize Java for microservices. Through its specifications, developers can create portable microservices applications that can run seamlessly across various runtimes. Eclipse MicroProfile is more about defining standards and specifications. This ensures that the microservices built with it are not tied to a specific tool or library so that the application could run across multiple environments and runtimes without hassle.

It provides a unified configuration API, that allows the configurations to be sourced from the different locations and gives the application more flexibility in managing microservice configurations. It includes such specifications that provide strategies to handle the failure of services.

It also follows with the JWT Security that helps in securing microservices by generating a tokenization which authenticates and authorizes the new user every time.

Key Benefits:

1. Microservices which are developed with Eclipse MicroProfile that can run across the different environments without modification.
2. It optimizes the microservices with its outstanding features and specifications.
3. It provides great management and flexibility for the developers in managing and adjusting their microservices.
4. It’s easy to integrate MicroProfile applications with other systems and services.

## FastAPI (Python)

FastAPI is a modern, web framework for building APIs with Python. It’s designed to create RESTful services with minimal effort, FastAPI automatically generates interactive API documentation, offers data validation, and more. FastAPI is its use of Python type hints in function signatures also this enables automatic data validation, serialization, and documentation generation.

FastAPI is built on Starlette for the web parts as it makes it more powerful for writing asynchronous tasks with Python ‘async/await’. FastAPI provides automatic interactive API documentation using tools such as Swagger and ReDoc, which makes it easier for developers to test and understand the APIs, whereas the FastAPI includes security and authentication as a main feature.

Key Benefits:

1. FastAPI applications are extraordinarily fast.
2. It’s easy to pick up and start developing.
3. Developers can achieve more with less code as it enhance productivity.
4. FastAPI represents a modern approach to web APIs development in Python which emphases the performance, simplicity, and its automatic features.

## Helidon Oracle’s Dual-Purpose Java Solution for Microservices

It’s a collection of Java libraries that are specifically built for the microservices. Helidon MP follows the MicroProfile specification, that gives Jakarta/Java EE to developers and makes easy way to create microservices. It provides a flexible configuration to the system that supports a large number of formats and sources. It also has a built-in security framework which can integrate with both SE and MP versions.

Key Benefits:

1. Helidon offers two styles: a lightweight version and a classic Java version.
2. Helidon is really fast and can handle many tasks at once.
3. Helidon aligns with a popular Java standard called MicroProfile, which means it’s in sync with best practices and the larger Java community.
4. With support from a big tech company like Oracle, Helidon is well-documented, well-supported, and will continue to evolve.

## Lagom (Scala and Java)

Lagom is a framework or a toolbox which builds highly strong systems of Reactive microservices which specifically targets to Scala and Java languages, it builds systems which give quick responses and remain stable even under such challenging conditions. It ensures that systems must be highly responsive, robust, strong, and scalable, that offer seamless user experiences even when dealing with various failures or high loads during runtime.

Key Benefits:

1. Lagom’s core design ensures that built systems offer quick or swift responses.
2. Lagom offers a development environment that provides hot-reloading, making the developer’s feedback loop faster.
3. Lagom is built for flexible deployment scenarios.
4. It has a strong ecosystem of libraries, tools.

## Quarkus (Java)

Quarkus is the modern Java framework which is developed for creating the microservices suitable for the Kubernetes, it provides a platform that manages containerized applications. It boosts a quick startup time and reduces runtime memory consumption for optimized performance. Quarkus works well with both OpenJDK HotSpot and GraalVM’s ahead-of-time compilation.

While it’s more compatible with the standard Java VM, its support for GraalVM ensures the faster startups and low memory usage. This efficiency makes it ideal for cloud settings where resource saving and scalability matter most.

Key Benefits:

1. Quarkus creates a dynamic development model.
2. Quarkus offers a broad spectrum of technologies, frameworks, and APIs, making it versatile and developer-friendly.
3. Built for both JVM and native compilation and it ensures the applications run efficiently and responsively.
4. Quarkus uses memory wisely, which makes sure it doesn’t waste any.


# Types of Microservices
Microservices can be categorized into two broad types – stateless and stateful microservices.

1. Stateless microservices:
    
  These are the building blocks of distributed systems. They don’t maintain or store any session state between two requests, hence the name “stateless” microservices. In addition, even if a service instance is removed, the service’s overall processing logic is not affected. This is why distributed systems leverage stateless microservices.

2. Stateful microservices:

Stateful microservices maintain or store session states or data in the code. Microservices communicating with each other always maintain service requests.
Stateless microservices are used more widely, but you can use stateful for multiple scenarios.

For example, suppose a customer places an order. Here “order” represents a microservice. So, the order service starts checking the product status using another service – inventory. When each request is independent of future or previous requests, this means the system follows a stateless architecture.

When you try to fetch the product information through a call, you will get the same result irrespective of the previous requests or context. And even if an order fails, it will not jeopardize the overall business processing. Another microservice will be ready to keep the process running.


Are Microservices RESTful?

Well, not necessarily. Let’s briefly review the differences:

Microservices: This is a collection of functions and services acting as building blocks of an application.
RESTful APIs: These represent the protocols, commands, and rules for integrating all the microservices into one single application.
Microservices is about an application’s design style and architecture, and you can build microservices with or without using a RESTful API. That said, using RESTful will make it a lot easier to develop loosely coupled microservices.

RESTful API came into being before microservices. It assumes that all objects have uniform interfaces and are completely language agnostic and loosely coupled. Here, the semantics and interfaces remain the same, and API implementation can change easily anytime without affecting consumers. Therefore, RESTful and microservices may solve different problems; they can still work together.

https://kinsta.com/blog/microservices-vs-api/
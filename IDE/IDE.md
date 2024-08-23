- [**JetBrains IntelliJ IDEA Ultimate**](#jetbrains-intellij-idea-ultimate)
- [**JetBrains PyCharm Professional**](#jetbrains-pycharm-professional)
- [**Visual Studio Professional**](#visual-studio-professional)
- [**Visual Studio Code (VS Code)**](#visual-studio-code-vs-code)
- [Eclipse Che](#eclipse-che)
- [Eclipse Theia](#eclipse-theia)
- [Additional Links and references](#additional-links-and-references)

# **JetBrains IntelliJ IDEA Ultimate**

- **Primary Purpose**: IntelliJ IDEA is a comprehensive **Java and Kotlin IDE** that provides everything you need out of the box. It caters to Java developers and offers a wide range of features for efficient coding, debugging, and project management.
- **Languages Supported**:
  - Java
  - Groovy
  - Kotlin
  - Scala
  - Python
  - Cython
  - Ruby and JRuby
  - Rust
  - PHP
  - Go
  - Dart
  - SQL
  - HTML, XML, JSON, YAML
  - JavaScript, TypeScript
  - CSS, Sass, SCSS, Less
  - Haml, Slim, Liquid
- **Framework Support**:
  - Spring (including Spring MVC, Spring Boot, Spring Integration, Spring Security, and more)
  - Java EE (JSF, JAX-RS, CDI, JPA, etc.)
  - Jakarta EE (JSF, JAX-RS, CDI, JPA, etc.)
  - Micronaut, Quarkus, Helidon
  - Hibernate, JPA
  - Ktor
  - JavaFX
  - Swing (including UI Designer)
  - Android (includes Android Studio functionality)
  - GWT
  - Thymeleaf, Freemarker, Velocity
  - Liquid, Go Template, Mustache, Qute
  - AspectJ, OSGI
  - Akka, SSP, Play2
  - React, React Native
  - Angular
  - Node.js, Next.js, Vue.js
  - Apache Flex, Adobe AIR
  - Ruby on Rails, Django, Flask, FastAPI
  - PyQT, Jupyter Notebook
  - Drupal, Wordpress
  - Laravel, Symfony
  - Twig, Blade
- **Integrated Developer Tools**:
  - Embedded Terminal
  - Database Tools
  - HTTP Client
  - Version Control (Git, GitHub, GitLab, Subversion, Mercurial)
  - Deployment (Docker, Docker Compose, Kubernetes, Java application servers)
  - Collaboration and Teamwork (Settings synchronization via JetBrains Account, Space integration, Issue tracker integration)
  - Appearance Customization (Custom themes)
  - Remote Interpreters (SSH, Docker, WSL, Vagrant)

# **JetBrains PyCharm Professional**

- **Primary Purpose**: PyCharm is a specialized **Python IDE** designed specifically for Python developers. It focuses on data science and web development, providing an enhanced user experience (UX) for working with Python and its technologies.
- **Languages Supported** (Equivalent to IntelliJ IDEA Ultimate):
  - Java
  - Groovy
  - Kotlin
  - Scala
  - Python
  - Cython
  - Ruby and JRuby
  - Rust
  - PHP
  - Go
  - Dart
  - SQL
  - HTML, XML, JSON, YAML
  - JavaScript, TypeScript
  - CSS, Sass, SCSS, Less
  - Haml, Slim, Liquid
- **Integrated Developer Tools** (Equivalent to IntelliJ IDEA Ultimate):
  - Embedded Terminal
  - Database Tools
  - HTTP Client
  - Version Control
  - Deployment
  - Collaboration and Teamwork
  - Appearance Customization
  - Remote Interpreters

# **Visual Studio Professional**

- **Primary Purpose**: Visual Studio Professional is a comprehensive **IDE** developed by Microsoft. It supports multiple languages and platforms, including **C#**, **C++**, **Python**, **JavaScript**, and more. It is suitable for web, desktop, cloud, and mobile development.
- **Languages Supported**:
  - C#
  - C++
  - Python
  - JavaScript
  - TypeScript
  - HTML, CSS
  - SQL
  - Go
  - Ruby
  - Swift (with extensions)
  - PHP (with extensions)
  - Java (with extensions)
  - R (with extensions)
  - Rust (with extensions)
  - PowerShell (with extensions)
  - And more...
- **Key Features**:
  - Robust debugging and profiling tools
  - Integrated Git support
  - Azure cloud integration
  - Extensions for various frameworks (ASP.NET, Xamarin, Unity, etc.)
  - Collaborative development features
  - Extensive plugin ecosystem

# **Visual Studio Code (VS Code)**

- **Primary Purpose**: VS Code is a **lightweight, open-source code editor** developed by Microsoft. It is not a full-fledged IDE but offers a versatile solution for various programming languages and frameworks.
- **Languages Supported**:
  - Extensible support for numerous languages through extensions (JavaScript, TypeScript, Python, Java, C++, Go, Rust, and more).
- **Key Features**:
  - Lightweight and fast
  - Rich extension ecosystem
  - Integrated terminal
  - Git integration
  - IntelliSense (code completion and suggestions)
  - Debugging capabilities
  - Customizable themes and settings
  - Cross-platform (Windows, macOS, Linux)

In summary:

- **IntelliJ IDEA** is tailored for **Java development**.
- **PyCharm** is specialized for **Python development** with a focus on data science and web development.
- **Visual Studio Code** provides a more versatile solution, supporting multiple languages along with advanced features for web development, database management, and cloud integration¹²⁷.

Each of these tools caters to different developer needs, so the choice depends on your specific requirements and preferred programming languages.

# Eclipse Che

**Eclipse Che** is an open-source system designed to provision **containerized development environments**, including integrated development environments (IDEs), within **Kubernetes clusters**. Let's explore its key features:

1. **Workspace Provisioning**:
    - Eclipse Che allows you to create **developer workspaces** from Git repositories or samples.
    - Workspaces include their dependencies, including **embedded containerized runtimes**.
    - Developers can access these workspaces via a **browser**, eliminating the need for local IDE installations.

2. **Multi-User Remote Development Platform**:
    - Eclipse Che serves as a **multi-user remote development platform**.
    - It provides a **flexible RESTful webservice** for managing workspaces and collaboration.

3. **Cloud IDE**:
    - Eclipse Che includes an **online IDE** (integrated development environment) accessible via the browser.
    - Developers can write, debug, and build code directly within the cloud environment.

4. **Open Format and Devfile**:
    - Eclipse Che uses the **Devfile format** to define development workspaces.
    - Devfiles describe the workspace configuration, including language runtimes, tools, and extensions.
    - The Devfile project is a collaboration between Red Hat, AWS, and JetBrains.

In summary, Eclipse Che streamlines development workflows by providing cloud-native, repeatable, and software-defined development environments. It's an excellent choice for teams working with Kubernetes and seeking efficient, collaborative development experiences

```
https://devspaces.apps.mloriedo-devworkspaces.devcluster.openshift.com/#https://github.com/devfile/api?che-editor=https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-code/insiders/devfile.yaml

https://<devspaces-hostname>#<git-repository-url>?che-editor=<editor-definition-url>

https://devspaces.apps.mloriedo-devworkspaces.devcluster.openshift.com/#https://github.com/devfile/api?che-editor=https://eclipse-che.github.io/che-plugin-registry/main/v3?che-editor=https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-code/insiders/devfile.yaml
```

Here is a list of available editors definitions in the Che plugin registry that can be used in the che-editor URL parameter:

| Editor                  | Link to the Che Plugin Registry definition |
| ----------------------- | ------------------------------------------ |
| Visual Studio Code      | [devfile.yaml](https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-code/insiders/devfile.yaml) |
| JetBrains IntelliJ IDEA | [devfile.yaml](https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-idea/latest/devfile.yaml) (latest version) <br> [devfile.yaml](https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-idea/next/devfile.yaml) (next most recent version) |
| JetBrains PyCharm       | [devfile.yaml](https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-pycharm/latest/devfile.yaml) (latest version) <br> [devfile.yaml](https://eclipse-che.github.io/che-plugin-registry/main/v3/plugins/che-incubator/che-pycharm/next/devfile.yaml) (next most recent version) |

# Eclipse Theia

**Eclipse Theia** is a **free and open-source framework** designed for building **web- and cloud-based tools** and **integrated development environments (IDEs)**. Let's explore its key features and use cases:

1. **Framework for Building Tools and IDEs**:
   - **Eclipse Theia** provides a platform to create custom tools and IDEs.
   - It is implemented in **TypeScript** and reuses components from **Visual Studio Code (VS Code)**.
   - Theia emphasizes **extensibility** and allows developers to build fully customized or white-labeled products.
   - Unlike some other open-source projects, Theia is **vendor-neutral** and hosted by the **Eclipse Foundation**, ensuring community-driven development².

2. **Use Cases**:
   - **Custom Language Support**: Developers can create tools with specific language support or domain-specific languages (DSLs).
   - **Graphical Modeling Tools**: Theia is suitable for building graphical modeling tools.
   - **Data-Centric Configuration UIs**: It can be used to create user interfaces for configuring data-centric applications.
   - **Git Integration**: Theia includes reusable base features, such as Git integration³.

3. **Frontend and Backend**:
   - Every Theia-based application consists of two parts:
     - **Frontend**: Runs in a web browser.
     - **Backend**: Can be deployed locally (for desktop applications) or remotely (for cloud applications)⁴.

In summary, **Eclipse Theia** empowers developers to build flexible, web-based tools and IDEs, making it easier to create customized development environments tailored to specific needs.

# Additional Links and references

- [How to run VS Code with OpenShift Dev Spaces](https://developers.redhat.com/articles/2022/07/12/how-run-vs-code-openshift-dev-spaces#select_visual_studio_code_as_the_editor_of_a_dev_space_environment)

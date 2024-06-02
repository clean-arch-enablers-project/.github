## Welcome to the place where clean architecture is made easy. 
💡 Discover our artifacts. Understand our vision. Join the force.

<br>

Summary:

- 🧰 [The SDK](https://github.com/clean-arch-enablers-project#-the-sdk)
- 🛠️ [The tools](https://github.com/clean-arch-enablers-project#%EF%B8%8F-the-tools)
  - [cae-framework](https://github.com/clean-arch-enablers-project#%EF%B8%8F-cae-framework)
  - [cae-cli](https://github.com/clean-arch-enablers-project#-cae-cli)
  - [cae-utils](https://github.com/clean-arch-enablers-project#%EF%B8%8F-cae-utils)
  - [cae-service-catalog](https://github.com/clean-arch-enablers-project#-cae-service-catalog)
- 🛤️ [What's the vision?](https://github.com/clean-arch-enablers-project#%EF%B8%8F-whats-the-vision)
  - 🧩 [The _Angularization_ Concept](https://github.com/clean-arch-enablers-project#-the-angularization-concept)
  - 🛰️ [The _Satellites_ Concept](https://github.com/clean-arch-enablers-project#%EF%B8%8F-the-satellites-concept)
  - 🌠 [An SDK is born](https://github.com/clean-arch-enablers-project#-an-sdk-is-born)
- ⚡[Joining the force](https://github.com/clean-arch-enablers-project#joining-the-force)

<br>

## 🧰 The SDK

Clean Arch Enablers (CAE) is an SDK designed to facilitate the application of clean architecture during software development, starting initially from the Java world. By using the SDK, software is created in both modular and easy-to-read manners, enabling numerous possibilities by harnessing the foundational architecture principles.

- The components made for Java are available on the Maven Central Repository: [CAE on Maven](https://central.sonatype.com/namespace/com.clean-arch-enablers).
- Discussions about the SDK can be found here: [CAE Discussions](https://github.com/orgs/clean-arch-enablers-project/discussions).
- Task Board for tracking what's going on: [CAE Task Board](https://github.com/orgs/clean-arch-enablers-project/projects/1)
- Organization page on LinkedIn: [CAE on Linkedin](https://www.linkedin.com/company/clean-arch-enablers)
- Organization page on Postman: [CAE on Postman](https://www.postman.com/clean-arch-enablers)

<br>

## 🛠️ The tools
State Symbol Key:

- ``✅`` — _Under release state_
- ``✔️`` — _Under snapshot state_
- ``⏳`` — _Under full development state_

<br>

### ``✔️`` [cae-framework](https://github.com/clean-arch-enablers-project/cae-framework)

From the CAE perspective, clean architecture leverages hexagonal principles by using the Ports & Adapters concept and mixing it with a flavor of the Vertical Slice pattern, componentizing an application by its use cases. This is the premise behind the cae-framework: shaping an application based on the axis of its use cases and plugging satellites around them as needed. This is the _angularization_ effect.

### ``⏳`` [cae-cli](https://github.com/clean-arch-enablers-project/cae-cli)

Angularizing the application with modularity can require a lot of effort if done manually, but with the CLI tool, one can generate articulated components with just one command, instead of having to create each folder and file by hand.

### ``✔️`` [cae-utils](https://github.com/search?q=topic%3Acae-utils+org%3Aclean-arch-enablers-project&type=Repositories)

Utility libraries for addressing common needs and establishing expected patterns throughout the CAE SDK components.

- ``✔️`` [cae-utils-mapped-exceptions](https://github.com/clean-arch-enablers-project/cae-utils-mapped-exceptions)
- ``✔️`` [cae-utils-env-vars](https://github.com/clean-arch-enablers-project/cae-utils-env-vars)
- ``✔️`` [cae-utils-trier](https://github.com/clean-arch-enablers-project/cae-utils-trier)
- ``✔️`` [cae-utils-http-client](https://github.com/clean-arch-enablers-project/cae-utils-http-client)

Each library is designed to be standalone, so client applications don't have to meet any prerequisites to use them. More libraries are on the way, as many common needs are still unaddressed.

### ``⏳`` [cae-service-catalog](https://github.com/clean-arch-enablers-project/cae-service-catalog) 

Since CAE client applications are highly angularized, a common pattern can be expected in all of them: they all have Use Case, Adapter, and Assembler instances, which are base components of the _cae-framework_. With this established, a process designed to run during the CI/CD phase can automatically identify, extract, and centralize all of the use case metadata a CAE client application has. This process can be applied across all applications within an organization, ensuring consistent availability of use case information. No more getting the runaround for members of organizations that use the CAE SDK. This is the premise of the real-time Service Catalog tool.

The goal is to automatically catalog all domains and their respective use cases in a centralized repository, ensuring that documentation is naturally generated and accessible to everyone within an organization, thereby neutralizing the Silo Effect that some companies unfortunately experience.

<br>

## 🛤️ What's the vision?

What's behind CAE as a philosophical instance is the idea of having applications follow a well-defined structure and a well-established pattern. This ensures that developers can understand what's going on, even if they are new to a team that has been developing the applications. This is the advantage of using a well-known framework. However, that's not the only thing CAE is about: the concept of having that and also being able to develop applications in a manner that avoids locking them to a specific system architecture or cloud provider is at the core of CAE philosophy. The famous architectural manifesto that aims to achieve this is the Clean Architecture and this is why this SDK is being built around its principles.

### 🧩 The _Angularization_ Concept

By harnessing Clean Architecture principles, the final result is applications that are angularized around the axis of the Vertical Slice pattern. But what is _Angularization_? — one might ask. The concept is already well-known, but is usually adopted as an afterthought rather than as a primary axis.

Breaking down the concept:

Code is solely written to make a programmable machine execute something. As developers write code, the simplest approach is to create straight sequences of commands without any cohesion. Such sequences don't add up to any coherent structure. The overall picture is loosely defined, without form. This might be acceptable for small applications, but for larger ones, it is not ideal. The angularization effect involves transforming code into well-defined components with clear boundaries, giving it a sense of shape or tangible entity. Metaphorically, elements stop being vague and formless and start having angles, hence the term 'angularization.'

This concept is well addressed by OOP, microservices' domain boundaries, DDD, and the list goes on. As mentioned, this is a well-known concept. However, the specific implementation of it in the CAE SDK is that the axis of angularized components is the use case elements of an application. It is not just about creating random OOP objects or _macro microservices_; it is about establishing that the axis are the features of an application, its functionalities, a.k.a its use cases.

With this concept implemented around the Vertical Slice approach, it is possible to achieve flexibility in how to dispatch functionalities of an application since they are their own distinct components. Will they be dispatched as REST API endpoints? AWS Lambda functions? Kafka consumers? Libraries for BFFs? This can be explored, enabling teams to experiment and innovate with less effort focused on building entire PoCs from scratch. This is what is referred to as "angularized applications".

### 🛰️ The _Satellites_ Concept

In addition to Angularization, there's the satellite concept, which involves creating auxiliary components that orbit around the main angularized functionalities. These satellites can provide supplementary features such as logging, monitoring, input validation, cache management, or even autodocumentation processes, without interfering with the core application logic.

This, by the way, exemplifies the application of the Open/Closed principle, from SOLID. 

### 🌠 An SDK is born

For these conceptual ideas to be implemented, a lot of effort would be necessary. This can prevent teams from deciding to work with such concepts. While this is understandable, the thought of letting all of these potential advantages slip away due to a lack of resources is troubling. This is why the Clean Arch Enablers (CAE) was born: to enable teams to apply the architecture and explore its potential.

Step by step, we will be delivering new pieces of software that will make the experience effortless, while also enabling the ability to take advantage of the benefits.

<br>

## ⚡Joining the force

If anyone gets interested in becoming an official collaborator, it is very simple. The workflow is:

1. 🔍 Identify where it would be interesting to collaborate.
2. 📝 Open a new issue on the repository that has an opportunity for enhancement, specifying the intended changes.
3. 🛠️ Create a new branch to address the changes (allowed prefixes: _feature/_, _refact/_, _docs/_, or _fix/_).
5. 📩 Once it is done, open a new pull request.
6. 🔀 Pass through the code review phase.
7. ✅ Welcome to the team!

Once completed the workflow, the developer gets associated with our GitHub and LinkedIn organization pages.

<br>
<br>
<br>
<br>

<p align="center">
  CAE — Clean Architecture made easy.
</p>

## Welcome to the place where clean architecture is made easy. 
💡 Discover our artifacts. Understand our vision. Join the force.

<br>

Symbol Key:

- ``✅`` — _Release state_
- ``✔️`` — _Snapshot state_
- ``⏳`` — _Under full development state_

<br>

## 🧰 The SDK

Clean Arch Enablers (CAE) is an SDK designed to facilitate the application of clean architecture during software development, starting initially from the Java world. By using the SDK, software is created in both modular and easy-to-read manners, enabling numerous possibilities by harnessing the foundational architecture principles.

<br>

## 🛠️ The tools

### ``✔️`` [cae-framework](https://github.com/clean-arch-enablers-project/cae-framework)

From the CAE perspective, clean architecture leverages hexagonal principles by using the Ports & Adapters concept and mixing it with a flavor of the Vertical Slice pattern, componentizing an application by its use cases. That's the premise behind the _cae-framework_: shaping an application based on the axis of its use cases and plugging satellites around them as needed. 

### ``✔️`` [cae-cli](https://github.com/clean-arch-enablers-project/cae-cli)

Angularizing the application with modularity can require a lot of effort if done manually, but with the CLI tool, one can generate articulated components with just one command, instead of having to create each folder and file by hand.

### ``✔️`` [cae-utils](https://github.com/search?q=topic%3Acae-utils+org%3Aclean-arch-enablers-project&type=Repositories)

Utility libraries for addressing common needs and establishing expected patterns throughout the CAE SDK components.

- ``✔️`` [cae-utils-mapped-exceptions](https://github.com/clean-arch-enablers-project/cae-utils-mapped-exceptions)
- ``✔️`` [cae-utils-env-vars](https://github.com/clean-arch-enablers-project/cae-utils-env-vars)
- ``✔️`` [cae-utils-trier](https://github.com/clean-arch-enablers-project/cae-utils-trier)
- ``✔️`` [cae-utils-http-client](https://github.com/clean-arch-enablers-project/cae-utils-http-client)

Each library is designed to be standalone, so client applications don't have to meet any prerequisites to use them. More libraries are on the way, as many common needs are still unaddressed.

### ``⏳`` [cae-service-catalog](https://github.com/clean-arch-enablers-project/cae-service-catalog) 

Since CAE client applications are highly angularized, a common pattern can be expected in all of them: they all have Use Case, Adapter, and Assembler instances, which are base components of the _cae-framework_. With this established, a process designed to run during the CI/CD phase can automatically identify, extract, and centralize all of the use cases a CAE client application has. This process can be applied across all applications within an organization, ensuring consistent availability of use case information. No more getting the runaround for members of organizations that use the CAE SDK. This is the premise of the real-time Service Catalog tool.

The goal is to automatically catalog all domains and their respective use cases in a centralized repository, ensuring that documentation is naturally generated and accessible to everyone within an organization, thereby neutralizing the Silo Effect that some companies unfortunately experience.

<br>

## 🛤️ What's the vision?

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->

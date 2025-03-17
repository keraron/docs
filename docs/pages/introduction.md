## Introduction

The AGNTCY is an open source collective building the infrastructure for The Internet of Agents: an open, interoperable internet for agent to agent collaboration.

### Vision

Agentic AI will accelerate all of human work. Enterprises need to create agentic workflows and applications combining internal and third-party agents to fully leverage the power of AI, accelerate their business, and achieve significant productivity gains.
We believe that an open, interoperable Internet of Agents is the key to enabling the best possible path forward to accelerate innovation and create the most value for all participants, from builders to operators, developers to consumers across all industries and businesses.

### Mission

We are an open source collective commited to build the Internet of Agents to be accessible to all. Our mission is to build a diverse, collaborative space to innovate, develop, and maintain software components and services that solve key problems in the domain of agentic workflows and multi-agent applications.

### Capabilities

Based on advanced protocols, frameworks, and components, the goal of IoA software infrastructure is to enable and simplify the creation of multi-agent applications through the following steps:

1. **DISCOVER**: Find and evaluate the best agents for the job.
1. **COMPOSE**: Connect agents into effective workflows across any framework or vendor.
1. **DEPLOY**: Run multi-agent systems at scale, securely.
1. **EVALUATE**: Monitor performance and improve efficiency and efficacy over time.

### Technical Objectives

1. **Interoperability**: Establish a common protocol that enables AI agents from different vendors and platforms to communicate and work together efficiently.
2. **Security**: Ensure secure interactions between agents through robust authentication, authorization, and encryption mechanisms.
3. **Scalability**: Design a scalable architecture that leverages the cloud-native stack optiomally, supporting a growing number of agents and interactions without compromising performance.
4. **Standardization**: Develop standardized data models and schemas to ensure consistent data representation and validation across the ecosystem.

### Core Components

The initial set of IoA components and architecture is outlined below. As a very first step, we are sharing two important specifications: OASF and ACP. In a few weeks the collective will release code and documentation for most of the components. This is a starting point - as new members join and bring their contributions, the collective will continue to evolve and expand the IoA architecture, components, and interfaces.

```{image} ../_static/ioa_stack.png
:alt: Simplified Internet of Agent Stack
:width: 100%
:align: center
```

* **Open Agent Schema Framework (OASF)**: A framework providing standardized data models and schemas for representing and validating data exchanged between agents. OASF ensures data integrity and consistency across the Internet of Agents ecosystem. Current OASF repo can be found [here](https://github.com/agntcy/oasf), OASF schema documentation can be found [here](https://schema.oasf.agntcy.org).
* **Agent Directory**: A centralized service that stores and manages OASF metadata about AI agents, including their capabilities, attributes, and constraints. This service enables agents to discover and connect with each other based on *their advertised capabilities.
* **Decentralized Agent Identity**: A system leveraging decentralized technologies to manage and verify the identities of agents issued by any organization, ensuring secure and trustworthy interactions.
* **Agent Connect Protocol (ACP)**: A protocol defining a standard interface to invoke agents, configure them, provide input, and retrieve output. The current ACP spec can be found [here](https://spec.acp.agntcy.org/).
* **Multi-Agent Application Toolkit**: A set of agents and software libraries supporting seamless creation of Multi-Agent workflows. 
* **Agent Workflow Server**: A server leveraging the ACP to manage and execute workflows involving multiple AI agents, ensuring coordinated and efficient task execution.
* **Agent Manifest**: A standard format describing agents, their capabilities, their dependencies, and how to deploy or consume them. The manifest is designed to be used by ACP and the Workflow Server and stored in the Agent Directory with the corresponding OASF extensions.
* **Agent Gateway Protocol (AGP)**: A protocol defining the standards and guidelines for secure and efficient network-level communication between AI agents. The AGP ensures interoperability and seamless data exchange by specifying message formats, transport mechanisms, and interaction patterns.
* **Agent Observability**: Tools and techniques for monitoring and analyzing the behavior and performance of AI agents, providing insights into their operations and interactions.

The following diagram shows a simplified architecture of the core components described above.


```{image} ../_static/ioa_arch.png
:alt: Simplified Internet of Agent Stack
:width: 100%
:align: center
```

### Benefits

* **Enhanced Collaboration**: By enabling seamless communication and data exchange, IoA fosters collaboration between AI agents, leading to more sophisticated and integrated solutions.
* **Improved Efficiency**: Standardized protocols and frameworks reduce the complexity of integrating diverse AI agents, resulting in faster development and deployment of AI-driven applications.
* **Increased Security**: Robust security mechanisms ensure that interactions between agents are secure, protecting sensitive data, and preventing unauthorized access.
* **Future-Proof Architecture**: The scalable and flexible design of IoA ensures that the ecosystem can grow and adapt to future advancements in AI technology.

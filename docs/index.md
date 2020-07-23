# Introduction

Figurate is a framework for building robust, scalable and modular Java applications. Figurate is based on the principles
of modularity, testability and adaptability, which provide the basis for a platform that is capable of supporting the
processes that underpin many of the modern design and implementation strategies.

## Modularity

As software projects evolve and mature they become more and more complex, leading to issues of maintainability and
comprehension. Often the design of many aspects of software is only fully understood by those that implement it.
Modularity ensures that software components are small enough to be understood by newcomers, resulting in an increased
maintainability, irregardless of how complex the software project is as a whole.

Additionally, modularity provides the opportunity to replaces individual software components as the need arises without
needing to rework the entire platform. Often there is a need to alter the integration points of a platform (e.g. datasources,
external services, etc.), or even to support multiple different integrations points concurrently. Either scenario is
well-supported by a modular approach, whereby a configuration change is all that is required.

## Testability

The quality of software is often in direct correlation to the amount of testing performed on it. Testing modular software
components allows more targeted and thorough testing of the features specific to the component. In addition, where a
Design by Contract approach is applied, testing provides verification that a component conforms to the required contracts.

## Adaptability

Often the final implementation of a software project is quite different to that which was envisaged prior to implementation.
Throughout the development process many changes may occur, both in architecture and underlying technologies. A modular
design supports these anticipated changes by allowing multiple different architectural and technological designs.


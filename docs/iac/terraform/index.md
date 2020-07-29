# Introduction

Terraform is an infrastructure provisioning tool that defines a language that
allows for declarative definition of infrastructure resources in a concise format.

The power of Terraform is in the extensive list of provider plugins that implement
integration with multiple Cloud platforms and vendor services.

## Pros

The primary benefit of Terraform is that it is the default standard for integration with
major Cloud providers, such that many of the new platform features are available first in Terraform.

* Strong Open Source community supporting multiple provider implementations for major and niche platforms and services.
* Extensible to support custom provider implementations to suit most needs
* Concise language well suited to infrastructure definition

## Cons

Terraform alone is usually not sufficient to provide a complete Infrastructure as Code solution, but can be the
cornerstone in a broader strategy.

* Infrastructure state is not managed automatically (Terraform Cloud does improve this)
* Some values may be stored in state insecurely (e.g. secrets, credentials, etc.)

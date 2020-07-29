# Infrastructure as Code

Cloud platforms are the cornerstone of a revolution in how we manage infrastructure.
The ability to provision just-in-time infrastructure programmatically means that we
are now able to "program" our infrastructure requirements to meet immediate and future
needs.

Infrastructure as code is the tooling and languages that allow us to interact with
IaaS, PaaS and Saas Cloud platforms to provision and manage infrastructure.

## Blueprints

Infrastructure as code (IaC) blueprints provide common, reusable code that may be
used in multiple architectures. Blueprints provide a number of benefits. 

### Abstraction

They allow us to abstract the details specific to
a platform or environment such that we only need to configure the instance-specific
details.

### Portability

By using higher-level constructs like blueprints to compose architectures we also inherently
improve the portability of an architecture, both within and across different platforms.

Blueprint-based architectures are more easily replicated in different environments as they
are easier to duplicate. (*)

(*) Some blueprints may not be replicable across all platforms, but generally Cloud concepts
are transferable across multiple platforms.  

### Defaults

We can use blueprints to provide sensible defaults as applicable to platforms and environments
we manage.

### Governance

Blueprints provde a way to enforce standards and governance around aspects such as
security, cost and availability. 

# Introduction

The Open Services Gateway Initiative (OSGi) is a plugin architecture
for composing Java applications. OSGi is an "in-process" approach to
modularity such that components share the same application memory, etc.

## Pros

The benefits of an in-process modular architecture like OSGi typically
relate to performance and security concerns.

* In-process communication is more efficient than out-of-process
* Less need for authentication/encryption of inter-component communication

## Cons

Whilst OSGi may be considered as a modular architecture it is still a more
tightly-coupled approach than out-of-process architectures.

* Restricted to Java-based components that can run in-process
* Whilst theoretically can replace/upgrade components at run-time, still requires redeploy for major changes
 
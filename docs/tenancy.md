# Tenancy management

Tenancy management is concerned with how you manage access and isolation of workloads.

## Maturity Model

Tenancy maturity considers the complexity of a hosting platform, and ability to support
secondary concerns. For example, Digital Ocean is a public Cloud platform without
fine-grained auditing, so it may be classified as a low maturity platform. For some
workloads this may be sufficient, but for others a more sophisticated (and costly) target 
platform may be required.

* Low - Network Isolation
* Medium - Role-based Access
* High - Auditing and Logging support

## References:

* [AWS Well Architected - Account Management and Separation](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/aws-account-management-and-separation.html)

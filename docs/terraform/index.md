# Figurate Terraform

## Introduction

Terraform is an infrastructure provisioning tool that defines a language that
allows for declarative definition of infrastructure resources in a concise format.

The power of Terraform is in the extensive list of provider plugins that implement
integration with multiple Cloud platforms and vendor services.

### Pros

The primary benefit of Terraform is that it is the default standard for integration with
major Cloud providers, such that many of the new platform features are available first in Terraform.

* Strong Open Source community supporting multiple provider implementations for major and niche platforms and services.
* Extensible to support custom provider implementations to suit most needs
* Concise language well suited to infrastructure definition

### Cons

Terraform alone is usually not sufficient to provide a complete Infrastructure as Code solution, but can be the
cornerstone in a broader strategy.

* Infrastructure state is not managed automatically (Terraform Cloud does improve this)
* Some values may be stored in state insecurely (e.g. secrets, credentials, etc.)

### Module Guidelines

Figurate modules are developed with the following principles for maximum portability and reuse.

* Use consistent variable names where possible (use same convention as resources)
* Make no assumptions about provider and backend configuration (these are configured externally)
* Avoid nested modules (minimise externally managed dependencies)
* Make no assumptions about execution environment (avoid local-exec)

### Figurate Modules

[Alerting](#alerting) | [API Gateway](#api-gateway) | [Audit Trail](#audit-trail) | [Autoscaling](#autoscaling) | [Backups](#backups) | [Budgets](#budgets) | [Cache](#cache) | [Canary Deployments](#canary-deployments) | [CDN](#cdn) | [Cell-based Architecture](#cell-based-architecture) | [Certificates](#certificates) | [Containers](#containers) | [Service Discovery](#service-discovery) | [Encryption](#encryption) | [Events](#events) | [Health Checks](#health-checks) | [Identity Management](#identity-management) | [Immutable Infrastructure](#immutable-infrastructure) | [Lifecycle Policies](#lifecycle-policies) | [Load Balancing](#load-balancing) | [Logging](#logging) | [Monitoring](#monitoring) | [Private Networking](#private-networking) | [Observability](#observability) | [Patching](#patching) | [Permissions](#permissions) | [Proxies](#proxies) | [Queues](#queues) | [Service Quotas](#service-quotas) | [Recovery](#recovery) | [Routing](#routing) | [Runbooks](#runbooks) | [Secrets](#secrets) | [Serverless](#serverless) | [Tenancy Management](#tenancy-management) | [Testing](#testing) | [Threat Detection](#threat-detection) | [Throttling](#throttling) | [Tracing](#tracing) | [Usage Monitoring](#usage-monitoring) | [Vulnerabilty Scans](#vulnerability-scans)

#### Alerting

* [AWS Cloudwatch Alarms](https://github.com/figurate/terraform-aws-cloudwatch-alarm)
* [AWS SNS Topics](https://github.com/figurate/terraform-aws-sns-topic)

#### API Gateway

* [AWS Lambda APIs](https://github.com/figurate/terraform-aws-lambda-api)
* [AWS API Gateway Targets](https://github.com/figurate/terraform-aws-apigateway-target)

#### Audit Trail

TBD.

#### Autoscaling

* [AWS Autoscaling Groups](https://github.com/figurate/terraform-aws-autoscaling-group)
* [AWS Launch Templates](https://github.com/figurate/terraform-aws-launch-template)

#### Backups

TBD.

#### Budgets

TBD.

#### Cache

TBD.

#### Canary Deployments

TBD.

#### CDN

* [DigitalOcean CDN](https://github.com/figurate/terraform-digitalocean-cdn)
* [AWS CloudFront Distribution](https://github.com/figurate/terraform-aws-cloudfront-distribution)

#### Cell-based Architecture

TBD.

#### Certificates

* [AWS ACM Certificates](https://github.com/figurate/terraform-aws-acm-certificate)

#### Containers

* [AWS ECS Clusters](https://github.com/figurate/terraform-aws-ecs-cluster)
* [AWS ECS Services](https://github.com/figurate/terraform-aws-ecs-service)
* [AWS ECS Task Definitions](https://github.com/figurate/terraform-aws-ecs-task-definition)
* [AWS ECR Repositories](https://github.com/figurate/terraform-aws-ecr-repository)
* [AWS ECS Capacity Providers](https://github.com/figurate/terraform-aws-ecs-capacity-provider)

#### Service Discovery

* [AWS Service Discovery Namespaces](https://github.com/figurate/terraform-aws-service-discovery-namespace)

#### Encryption

* [AWS KMS Keys](https://github.com/figurate/terraform-aws-kms-key)

#### Events

* [AWS S3 Triggers](https://github.com/figurate/terraform-aws-s3-trigger)
* [AWS CloudWatch Events](https://github.com/figurate/terraform-aws-cloudwatch-event)

#### Health Checks

TBD.

#### Identity Management

TBD.

#### Immutable Infrastructure

TBD.

#### Lifecycle Policies

TBD.

#### Load Balancing

* [AWS Load Balancing](https://github.com/figurate/terraform-aws-load-balancer)

#### Logging

TBD.

#### Monitoring

TBD.

#### Private Networking

* [AWS VPC Subnets](https://github.com/figurate/terraform-aws-vpc-subnets)
* [AWS VPC Endpoints](https://github.com/figurate/terraform-aws-vpc-endpoints)
* [AWS Security Groups](https://github.com/figurate/terraform-aws-security-group)
* [DigitalOcean Firewalls](https://github.com/figurate/terraform-digitalocean-firewall)

#### Observability

TBD.

#### Patching

TBD.

#### Permissions

* [AWS IAM Roles](https://github.com/figurate/terraform-aws-iam-role)
* [AWS IAM Groups](https://github.com/figurate/terraform-aws-iam-group)
* [AWS IAM Policies](https://github.com/figurate/terraform-aws-iam-policies)

#### Proxies

TBD.

#### Queues

TBD.

#### Service Quotas

TBD.

#### Recovery

TBD.

#### Routing

* [AWS Route53 Zones](https://github.com/figurate/terraform-aws-route53-zone)

#### Runbooks

TBD.

#### Secrets

* [AWS SSM Parameters](https://github.com/figurate/terraform-aws-ssm-parameters)

#### Serverless

* [AWS Lambda Layers](https://github.com/figurate/terraform-aws-lambda-layer)
* [AWS Lambda Functions](https://github.com/figurate/terraform-aws-lambda-function)

#### Tenancy Management

* [Github Repositories](https://github.com/figurate/terraform-github-repositories)
* [Github Organizations](https://github.com/figurate/terraform-github-organization)
* [Terraform Cloud Organizations](https://github.com/figurate/terraform-tfe-organization)
* [Terraform Cloud Workspaces](https://github.com/figurate/terraform-tfe-workspace)
* [AWS Organizations Policies](https://github.com/figurate/terraform-aws-organizations-policy)

#### Testing

TBD.

#### Threat Detection

TBD.

#### Throttling

TBD.

#### Tracing

TBD.

#### Usage Monitoring

TBD.

#### Vulnerability Scans

TBD.

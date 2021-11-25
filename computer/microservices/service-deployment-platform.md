---
type: microservices
layer: Infrastructure patterns
group: Deployment
---
# Service deployment platform

## Context

You have applied the [[Microservice architecture]] pattern and architected your system as a set of services. Each service is deployed as a set of service instances for throughput and availability.

## Problem

How are services packaged and deployed?

## Forces

- Services are written using a variety of languages, frameworks, and framework versions
- Each service consists of multiple service instances for throughput and availability
- Service must be independently deployable and scalable
- Service instances need to be isolated from one another
- You need to be able to quickly build and deploy a service
- You need to be able to constrain the resources (CPU and memory) consumed by a service
- You need to monitor the behavior of each service instance
- You want deployment to reliable
- You must deploy the application as cost-effectively as possible

## Solution

Use a deployment platform, which is automated infrastructure for application deployment. It provides a service abstraction, which is a named, set of highly available (e.g. load balanced) service instances.

## Examples

- [[Docker orchestration]] frameworks including [[Docker swarm]] mode and [[Kubernetes]]
- [[Serverless]] platforms such as [[AWS Lambda]]
- [[PaaS]] including [[Cloud Foundry]] and [[AWS Elastic Beanstalk]]

## Related patterns

- Some deployment platforms provide a [[Service Registry]] and [[Server-Side discovery]]
- Internally, a deployment platform might use use containers or virtual machines to deploy a service. Docker orchestration frameworks are, of course, explicitly container-based
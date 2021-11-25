---
type: microservices
layer: Infrastructure patterns
group: Deployment
---
# Single Service instance per host

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

Deploy each single service instance on its own host

## Examples

## Resulting context

The benefits of this approach include:
- Services instances are isolated from one another
- There is no possibility of conflicting resource requirements or dependency versions
- A service instance can only consume at most the resources of a single host
- Its straightforward to monitor, manage, and redeploy each service instance

The drawbacks of this approach include:
- Potentially less efficient resource utilization compared to [[Multiple Services per Host]] because there are more hosts

## Related patterns

- The [[Multiple Service Instances per Host]] pattern is an alternative solution
- The [[Service Instance per VM]] pattern is a refinement of this pattern
- The [[Service Instance per Container]] pattern is a refinement of this pattern
- The [[Serverless deployment]] pattern is an alternative solution.

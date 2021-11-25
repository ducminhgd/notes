---
type: microservices
layer: Application Infrastructure patterns
group: Security
---
# Access token

## Context

You have applied the [[Microservice architecture]] and [[API Gateway]] patterns. The application consists of numerous services. The [[API gateway]] is the single entry point for client requests. It authenticates requests, and forwards them to other services, which might in turn invoke other services.

## Problem

How to communicate the identity of the requestor to the services that handle the request?

## Forces

Services often need to verify that a user is authorized to perform an operation

## Solution

The [[API Gateway]] authenticates the request and passes an access token (e.g. [JSON Web Token](https://jwt.io/)) that securely identifies the requestor in each request to the services. A service can include the access token in requests it makes to other services.

## Example

See [JSON Web Token](https://jwt.io/) for usage examples and supporting libraries.

## Resulting context

This pattern has the following benefits:
- The identity of the requestor is securely passed around the system.
- Services can verify that the requestor is authorized to perform an operation

## Related patterns

The [[API Gateway]] uses this pattern.
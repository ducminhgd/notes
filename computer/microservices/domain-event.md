---
type: microservices
layer: Application patterns
group: Data patterns
subgroup: Maintaining data consistency
---
# Domain event

From [[Domain-Driven Design]] ([[DDD]]).

## Context

A service often needs to publish events when it updates its data. These events might be needed, for example, to update a [CQRS view](cqrs.md). Alternatively, the service might participate in an choreography-based [saga](saga.md), which uses events for coordination.

## Problem

How does a service publish an event when it updates its data?

## Solution

Organize the business logic of a service as a collection of DDD [aggregates](aggregate.md) that emit domain events when they created or updated. The service publishes these domain events so that they can be consumed by other services.

## Related patterns

- The [[Saga]] and [[CQRS]] patterns create the need for this pattern
- The [[Aggregate]] pattern is used to structure the business logic
- The [[Transactional outbox]] pattern is used to publish events as part of a database transaction
- [[Event sourcing]] is sometimes used to publish domain events

## See also

My book [Microservices patterns](https://microservices.io/book) describes this pattern in a lot more detail.

---
type: microservices
layer: Application Infrastructure patterns
group: Communication patterns
subgroup: Transactional messaging
---
# Transactional outbox

Also known as [[Application events]]

## Context

A service command typically needs to update the database and send messages/events. For example, a service that participates in a [[saga]] needs to atomically update the database and sends messages/events. Similarly, a service that publishes a [[domain event]] must atomically update an [[aggregate]] and publish an event.

A service must atomically update the database and send messages in order to avoid data inconsistencies and bugs. However, it is not viable to use a traditional distributed transaction (2PC) that spans the database and the message broker to atomically update the database and publish messages/events. The message broker might not support [[2PC]]. And even if does, it's often undesirable to couple the service to both the database and the message.

But without using [[2PC]], sending a message in the middle of a transaction is not reliable. There's no guarantee that the transaction will commit. Similarly, if a service sends a message after committing the transaction there's no guarantee that it won't crash before sending the message.

In addition, messages must be sent to the message broker in the order they were sent by the service. They must usually be delivered to each consumer in the same order although that's outside the scope of this pattern. For example, let's suppose that an aggregate is updated by a series of transactions `T1`, `T2`, etc. This transactions might be performed by the same service instance or by different service instances. Each transaction publishes a corresponding event: `T1 -> E1`, `T2 -> E2`, etc. Since `T1` precedes `T2`, event `E1` must be published before `E2`.

## Problem

How to reliably/atomically update the database and send messages/events?

## Forces

- 2PC is not an option
- If the database transaction commits messages must be sent. Conversely, if the database rolls back, the messages must not be sent
- Messages must be sent to the message broker in the order they were sent by the service. This ordering must be preserved across multiple service instances that update the same aggregate.

## Solution

A service that uses a relational database inserts messages/events into an outbox table (e.g. `MESSAGE`) as part of the local transaction. An service that uses a NoSQL database appends the messages/events to attribute of the record (e.g. document or item) being updated. A separate Message Relay process publishes the events inserted into database to a message broker.

![](ReliablePublication.png)

## Result context

This pattern has the following benefits:
- 2PC is not used
- Messages are guaranteed to be sent if and only if the database transaction commits
- Messages are sent to the message broker in the order they were sent by the application

This pattern has the following drawbacks:
- Potentially error prone since the developer might forget to publish the message/event after updating the database.

This pattern also has the following issues:
- The Message Relay might publish a message more than once. It might, for example, crash after publishing a message but before recording the fact that it has done so. When it restarts, it will then publish the message again. As a result, a message consumer must be idempotent, perhaps by tracking the IDs of the messages that it has already processed. Fortunately, since Message Consumers usually need to be idempotent (because a message broker can deliver messages more than once) this is typically not a problem.

## Related patterns

- The [[Saga]] and [[Domain event]] patterns create the need for this pattern.
- The [[Event sourcing]] is an alternative solution
- There are two patterns for implementing the message relay:
  - The [[Transaction log tailing]] pattern
  - The [[Polling publisher]] pattern

## Learn more

- My book [Microservices patterns](https://microservices.io/book) describes this pattern in a lot more detail.
- The [Eventuate Tram framework](https://github.com/eventuate-tram/eventuate-tram-core) implements this pattern
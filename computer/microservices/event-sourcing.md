---
type: microservices
layer: Application patterns
group: Data patterns
subgroup: Maintaining data consistency
---
# Event sourcing

## Context

A service command typically needs to update the database **and** send messages/events. For example, a service that participates in a [[saga]] needs to atomically update the database and sends messages/events. Similarly, a service that publishes a [[domain event]] must atomically update an [[aggregate]] and publish an event.

A service must atomically update the database and send messages in order to avoid data inconsistencies and bugs. However, it is not viable to use a traditional distributed transaction (2PC) that spans the database and the message broker to atomically update the database and publish messages/events. The message broker might not support [[2PC]]. And even if does, it’s often undesirable to couple the service to both the database and the message.

But without using 2PC, sending a message in the middle of a transaction is not reliable. There’s no guarantee that the transaction will commit. Similarly, if a service sends a message after committing the transaction there’s no guarantee that it won’t crash before sending the message.

In addition, messages must be sent to the message broker in the order they were sent by the service. They must usually be delivered to each consumer in the same order although that’s outside the scope of this pattern. For example, let’s suppose that an aggregate is updated by a series of transactions `T1`, `T2`, etc. This transactions might be performed by the same service instance or by different service instances. Each transaction publishes a corresponding event: `T1 -> E1`, `T2 -> E2`, etc. Since `T1` precedes `T2`, event `E1` must be published before `E2`.

## Problem

How to reliably/atomically update the database and send messages/events?

## Forces

- 2PC is not an option
- If the database transaction commits messages must be sent. Conversely, if the database rolls back, the messages must not be sent
- Messages must be sent to the message broker in the order they were sent by the service. This ordering must be preserved across multiple service instances that update the same aggregate.

## Solution

A good solution to this problem is to use event sourcing. Event sourcing persists the state of a business entity such an Order or a Customer as a sequence of state-changing events. Whenever the state of a business entity changes, a new event is appended to the list of events. Since saving an event is a single operation, it is inherently atomic. The application reconstructs an entity’s current state by replaying the events.

Applications persist events in an event store, which is a database of events. The store has an API for adding and retrieving an entity’s events. The event store also behaves like a message broker. It provides an API that enables services to subscribe to events. When a service saves an event in the event store, it is delivered to all interested subscribers.

Some entities, such as a Customer, can have a large number of events. In order to optimize loading, an application can periodically save a snapshot of an entity’s current state. To reconstruct the current state, the application finds the most recent snapshot and the events that have occurred since that snapshot. As a result, there are fewer events to replay.

## Example

[Customers and Orders](https://github.com/eventuate-examples/eventuate-examples-java-customers-and-orders) is an example of an application that is built using Event Sourcing and [[CQRS]]. The application is written in Java, and uses Spring Boot. It is built using [Eventuate](http://eventuate.io/), which is an application platform based on event sourcing and [[CQRS]].

The following diagram shows how it persist orders.

![](storingevents.png)

Instead of simply storing the current state of each order as a row in an `ORDERS` table, the application persists each `Order` as a sequence of events. The `CustomerService` can subscribe to the order events and update its own state.

Here is the `Order` aggregate:

```java
public class Order extends ReflectiveMutableCommandProcessingAggregate<Order, OrderCommand> {

  private OrderState state;
  private String customerId;

  public OrderState getState() {
    return state;
  }

  public List<Event> process(CreateOrderCommand cmd) {
    return EventUtil.events(new OrderCreatedEvent(cmd.getCustomerId(), cmd.getOrderTotal()));
  }

  public List<Event> process(ApproveOrderCommand cmd) {
    return EventUtil.events(new OrderApprovedEvent(customerId));
  }

  public List<Event> process(RejectOrderCommand cmd) {
    return EventUtil.events(new OrderRejectedEvent(customerId));
  }

  public void apply(OrderCreatedEvent event) {
    this.state = OrderState.CREATED;
    this.customerId = event.getCustomerId();
  }

  public void apply(OrderApprovedEvent event) {
    this.state = OrderState.APPROVED;
  }


  public void apply(OrderRejectedEvent event) {
    this.state = OrderState.REJECTED;
  }
```

Here is an example of an event handler in the `CustomerService` that subscribes to `Order` events:

```java
@EventSubscriber(id = "customerWorkflow")
public class CustomerWorkflow {

  @EventHandlerMethod
  public CompletableFuture<EntityWithIdAndVersion<Customer>> reserveCredit(
          EventHandlerContext<OrderCreatedEvent> ctx) {
    OrderCreatedEvent event = ctx.getEvent();
    Money orderTotal = event.getOrderTotal();
    String customerId = event.getCustomerId();
    String orderId = ctx.getEntityId();

    return ctx.update(Customer.class, customerId, new ReserveCreditCommand(orderTotal, orderId));
  }

}
```

It processes an `OrderCreated` event by attempting to reserve credit for the orders customer.

There are [several example applications](http://eventuate.io/exampleapps.html) that illustrate how to use event sourcing.

## Resulting context

Event sourcing has several benefits:
- It solves one of the key problems in implementing an event-driven architecture and makes it possible to reliably publish events whenever state changes.
- Because it persists events rather than domain objects, it mostly avoids the object‑relational impedance mismatch problem.
- It provides a 100% reliable [audit log](https://microservices.io/patterns/observability/audit-logging) of the changes made to a business entity
- It makes it possible to implement temporal queries that determine the state of an entity at any point in time.
- Event sourcing-based business logic consists of loosely coupled business entities that exchange events. This makes it a lot easier to migrate from a monolithic application to a microservice architecture.

Event sourcing also has several drawbacks:
- It is a different and unfamiliar style of programming and so there is a learning curve.
- The event store is difficult to query since it requires typical queries to reconstruct the state of the business entities. That is likely to be complex and inefficient. As a result, the application must use [[Command Query Responsibility Segregation]] ([[CQRS]]) to implement queries. This in turn means that applications must handle eventually consistent data.

## Related patterns

- The [[Saga]] and [[Domain event]] patterns create the need for this pattern.
- The [[CQRS]] must often be used with event sourcing.
- Event sourcing implements the [[Audit logging]] pattern.

## See also

- [Eventuate](http://eventuate.io/), which is a platform for developing applications with Event Sourcing and CQRS
- [Articles about event sourcing and CQRS](http://eventuate.io/articles.html)
- [How Eventuate implements snapshots](https://blog.eventuate.io/2017/03/07/eventuate-local-now-supports-snapshots/)
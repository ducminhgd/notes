---
type: design pattern
keywors:
- event driven
- event
- pattern
---

# Event Driven Design

## Event driven state machines concepts

- [[State machine]] - a state machine is a mathemetical asbstraction used to design algorithms base on behaviour model. It reads a set of inputs and change to a different state based on those inputs.
- [[State]] — A state is a description of the status of a system waiting to execute a transition.
- [[Transition]] — A transition is a change from one state to another. A transition is a set of actions to execute when a condition is fulfilled or an event received.
- [[Event]] — The entity that drives the state changes.
- Event-driven State Machine — A state machine is event-driven, if the transition from one state to another is triggered by an event or a message (not based on consuming/parsing characters or based on conditions).
- Event Driven Systems — Event Driven Architecture is about components communicating via publishing events rather than making (e.g.) RPC/CRUD calls against each other or manipulating shared state. It’s a communication strategy (albeit one which often relies on messages being persisted for fairly long periods until they are consumed).

## Benefits of using Event sourcing based Event Driven Systems

- Complete Rebuild: The application state can be discarded completely and rebuilt by re-running the events from the event log on that empty application state.
- Temporal Query: The application state can be determined at any point in time by starting with blank application state and then, rerunning the events up to a particular time or event.
- Event Replay: If any past event was incorrect, the consequences can be computed by reversing it and the later events and finally, replaying the new correct event and the later events. This can also, be achieved by blank application state and replaying the events after fixing them and their order.
- System Debug/Audit : The append-only storage of events in the event sourced journal provides an audit trail that can be used to monitor actions taken against a data store, regenerate the current state as materialized views or projections by replaying the events at any time, and assist in testing and debugging the system.

## Caveats to take care in Event Sourcing based Event Driven Systems

1. Eventual Consistency — The reads will not be based on the most recent writes of the events as there will be delay in between creating new materialized views or the projections of the data by event replaying. During the delay between publishing of the events, creating the materialized view and notifying the consumers, new events will have been written to the event journal.
2. Event Log Immutability — The event log acts as a single source of truth and needs to be immutable and thus, event data should never be updated. In order to update an entity to undo a change is to add a corresponding event to fix it. Even the event schema change needs to done on all the events in the event journal store.
3. Event Ordering and Linearity — As multiple applications or publishers will be creating and publishing events to the event store, the event ordering and linearity becomes important to maintain the data consistency. Adding a timestamp to every event or annotating each event resulting from a request with an incremental identifier resolves the issue of ordering conflict.
4. Consumer Idempotency — Event publication might be at least once (as keeping it exactly once will be difficult), and so consumers of the events must be idempotent. They must not reapply the update described in an event if the event is handled more than once so avoid unnecessary side-effects on the entity state or data attribute computation.
5. Snapshotting and Materialising — The events needs to be snapshotted and materialised on regular intervals, specially if the size of the event stream is large to handle on-demand queries on the model state and its attribute data.

## Difference between Event Sourcing and [[Change Data Capture]]

Change data events use the underlying database transaction log as the source of truth. The event is based on the database that the transaction log belongs to, rather than the original application and the event is available for as long as the events are persisted (not immutable) based on a mutable database, which means tighter coupling to the database data model. The CDC captures the effect of the event in terms of create, update or delete, for example, the button was update to off state.
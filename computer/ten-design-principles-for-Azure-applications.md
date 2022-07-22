---
type: architecture
keywords: azure, principles
---
# Ten design principles for Azure applications

## Design for self healing

> In a distributed system, failures happen. Design your application to be self healing when failures occur.

Requires a three-pronged approach:
- Detect failures.
- Respond to failures gracefully.
- Log and monitor failures, to give operational insight.

Should focus on handling local failures, short-lived failures, such as: network connectivity failures or failed database connections.

### Recommendations

- **Retry failed operations** for momentary or transient failure. Using [[Transient Fault Handling]] or [[Retry pattern]]
- **Protect failing remote services ([[Circuit Breaker]])**: avoid callers hammering a failing service if the failures persist.
- **Isolate critical resouces ([[Bulkhead]])** some failures may cascade to lead to resource exhaustion. To avoid this, partition a system into isolated groups, so that a failure in one partition does not bring down the entire system.
- **Perform load leveling**: use [[Queue-based Load Leveling Pattern]] to queue work items to run asynchronously to avoid sudden spikes.
- **Fail over**: if an instance cannot be reached, fail over to another instance. This my require the application to deal with eventual consistency.
- **Compensate failed transactions**: use [[Compensating Transactions]] to undo any step that already completed. Read more: [[Saga pattern]]
- **Checkpoint long-running transasctions**: Checkpoints can provide resiliency if a long-running operation fails.
- **Degrade gracefully**: simplify... sacrify some features for main features of application.
- **Throttle clients**. Sometimes a small number of users create excessive load, which can reduce your application's availability for other users. In this situation, throttle the client for a certain period of time. See the [[Throttling pattern]].
- **Block bad actors**: if a client consistently exceeds their quota or otherwise behaves badly, you might block them
- **Use [[leader election]]**.
- **Test with fault injection**: Use fault injection to test the resiliency of the system to failures, either by triggering actual failures or by simulating them
- **Embrace chaos engineering** Chaos engineering extends the notion of fault injection, by randomly injecting failures or abnormal conditions into production instances.

## Make all things redundant

> Build redundancy into your application, to avoid having single points of failure.

## Minimize coordination

> Minimize coordination between application services to achieve scalability.

## Design to scale out

> Design your application so that it can scale horizontally, adding or removing new instances as demand requires.

## Partition around limits

> Use partitioning to work around database, network, and compute limits.

## Design for operations

> Design your application so that the operations team has the tools they need.

## Use managed services

> When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).

### Use an identity service

> Use an identity as a service (IDaaS) platform instead of building or operating your own.

## Use the best data store for the job

> Pick the storage technology that is the best fit for your data and how it will be used.

## Design for evolution

> All successful applications change over time. An evolutionary design is key for continuous innovation.

## Build for the needs of business

> Every design decision must be justified by a business requirement.

## Read more

1. https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/
2. https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/self-healing

## Read next

1. https://docs.microsoft.com/en-us/azure/architecture/best-practices/transient-faults
2. https://docs.microsoft.com/en-us/azure/architecture/patterns/retry
3. https://docs.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling
4. https://docs.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction
5. https://docs.microsoft.com/en-us/azure/architecture/patterns/throttling
6. https://docs.microsoft.com/en-us/azure/architecture/patterns/leader-election
7. https://docs.microsoft.com/en-us/azure/architecture/framework/resiliency/principles
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

### Recommendations

- **Consider business requirements** The amount of redundancy built into a system can affect both cost and complexity. Your architecture should be informed by your business requirements, such as [[recovery time objective]] (RTO)
- **Place VMs/instances behind a load balancer** don't use single instance for workloads, use multiple-instances instead and place them behind a load balancer to distribute traffic to remaining healthy instances.
- **[[Partition]] for availability**. Database partitioning is often used to improve scalability, but it can also improve availability. If one shard goes down, the other shards can still be reached. A failure in one shard will only disrupt a subset of the total transactions.
- **Deploy to more than one region**. For the highest availability, deploy the application to more than one region
- **Synchronize front and backend failover**, [[Frontend]] can point or reach to [[Backend]] from another region when there is a failure in same region [[Backend]].
- **Use automatic failover but manual failback**. Use Traffic Manager for automatic [[failover]], but not for automatic [[failback]]. Automatic failback carries a risk that you might switch to the primary region before the region is completely healthy.
- **Include redundancy for Traffic Manager**. Traffic Manager is a possible failure point. Review the [[Traffic Manager]] [[SLA]], and determine whether using Traffic Manager alone meets your business requirements for [[high availability]]

## Minimize coordination

> Minimize coordination between application services to achieve scalability.

What happens when two instances try to perform concurrent operations that affect some shared state? In some cases, there must be coordination across nodes, for example to preserve [[ACID]] guarantees

You can use a pattern such as [[Scheduler Agent Supervisor]] to coordinate between the workers

### Recommendations

- **Embrace eventual consistency**. When data is distributed, it takes coordination to enforce strong consistency guarantees. Instead of putting it into a single transaction scope, it's better if the system can accommodate eventual consistency, perhaps using [[Compensating Transactions]] pattern.
- **Use domain events to synchronize state**. A [[domain event]] is an event that records when something happens that has significance within the domain. Interested services can listen for the event, rather than using a global transaction to coordinate across multiple services. If this approach is used, the system must tolerate eventual consistency (see previous item).
- **Consider patterns such as [[CQRS]] and [[event sourcing]]**. These two patterns can help to reduce contention between read workloads and write workloads.
- **Partition data**. Avoid putting all of your data into one data schema that is shared across many application services. A [[microservices]] architecture enforces this principle by making each service responsible for its own data store
- **Design idempotent operations**. When possible, design operations to be idempotent. That way, they can be handled using at-least-once semantics.
- **Use [[asynchronous]] [[parallel]] processing**. If an operation requires multiple steps that are performed asynchronously (such as remote service calls), you might be able to call them in parallel, and then aggregate the results. This approach assumes that each step does not depend on the results of the previous step.
- **Use [[optimistic concurrency]] when possible**. [[Pessimistic concurrency]] control uses [[database]] locks to prevent conflicts. This can cause poor performance and reduce availability. With optimistic concurrency control, each transaction modifies a copy or [[snapshot]] of the data. When the transaction is committed, the database engine validates the transaction and rejects any transactions that would affect database consistency.
- **Consider MapReduce or other parallel, distributed algorithms**. Depending on the data and type of work to be performed, you may be able to split the work into independent tasks that can be performed by multiple nodes working in parallel. See [[Big compute architecture]] style.
- **Use leader election for coordination** , Using the [[Leader Election]] pattern

## Design to scale out

> Design your application so that it can scale horizontally, adding or removing new instances as demand requires.

### Recommendations

- **Avoid instance stickiness**. Stickiness, or session affinity, is when requests from the same client are always routed to the same server
- **Identify bottlenecks**. Scaling out isn't a magic fix for every performance issue. For example, if your backend database is the bottleneck, it won't help to add more web servers. Identify and resolve the bottlenecks in the system first, before throwing more instances at the problem. Stateful parts of the system are the most likely cause of bottlenecks.
- **Decompose workloads by scalability requirements**. Applications often consist of multiple workloads, with different requirements for scaling.
- **Offload resource-intensive tasks**. Tasks that require a lot of CPU or I/O resources should be moved to background jobs when possible, to minimize the load on the front end that is handling user requests.
- **Use built-in autoscaling features**. Many Azure compute services have built-in support for autoscaling. If the application has a predictable, regular workload, scale out on a schedule. <-- *this may be ads from Azure*. For [[autoscaling]] best practices, [read more](https://docs.microsoft.com/en-us/azure/architecture/best-practices/auto-scaling)
- **Consider aggressive autoscaling for critical workloads**. For critical workloads, you want to keep ahead of demand. It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.
- **Design for scale in**. Remember that with elastic scale, the application will have periods of scale in, when instances get removed. The application must gracefully handle instances being removed. Here are some ways to handle scalein:
  - Listen for shutdown events (when available) and shut down cleanly.
  - Clients/consumers of a service should support [[transient fault]] handling and retry
  - For [[long-running]] tasks, consider breaking up the work, using checkpoints or the [[Pipes and Filters]] pattern.
  - Put work items on a queue so that another instance can pick up the work, if an instance is removed in the middle of processing.

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
2. https://docs.microsoft.com/en-us/azure/architecture/best-practices/transient-faults
3. https://docs.microsoft.com/en-us/azure/architecture/patterns/retry
4. https://docs.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling
5. https://docs.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction
6. https://docs.microsoft.com/en-us/azure/architecture/patterns/throttling
7. https://docs.microsoft.com/en-us/azure/architecture/patterns/leader-election
8. https://docs.microsoft.com/en-us/azure/architecture/framework/resiliency/principles
9. https://docs.microsoft.com/en-us/azure/architecture/patterns/scheduler-agent-supervisor
10. https://docs.microsoft.com/en-us/dynamicsax-2012/developer/optimistic-concurrency-control
11. https://docs.microsoft.com/en-us/azure/cosmos-db/sql/database-transactions-optimistic-concurrency
12. https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-compute
13. https://docs.microsoft.com/en-us/azure/architecture/patterns/pipes-and-filters
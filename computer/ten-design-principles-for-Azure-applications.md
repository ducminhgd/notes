---
type: architecture
keywords:
    - architecture
    - design
    - Azure
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

There are many ways to [[partition]] a system, such as:
- Partition a database to avoid limits on database size, data [[I/O]], or number of [[concurrent]] sessions.
- Partition a queue or message bus to avoid limits on the number of requests or the number of [[concurrent]] connections.
- Partition an App Service web app to avoid limits on the number of instances per App Service plan.

A database can be partitioned *horizontally*, *vertically*, or *functionally*.
- In horizontal partitioning, also called sharding, each partition holds data for a subset of the total data set. The partitions share the same data schema. For example, customers whose names start with A–M go into one partition, N–Z into another partition.
- In vertical partitioning, each partition holds a subset of the fields for the items in the data store. For example, put frequently accessed fields in one partition, and less frequently accessed fields in another.
- In functional partitioning, data is partitioned according to how it is used by each bounded context in the system. For example, store invoice data in one partition and product inventory data in another. The schemas are independent.

### Recommendations

- **Partition different parts of the application**. Databases are one obvious candidate for partitioning, but also consider storage, cache, queues, and compute instances.
- **Design the partition key to avoid hotspots**. If you partition a database, but one shard still gets the majority of the requests, then you haven't solved your problem. Ideally, load gets distributed evenly across all the partitions. The same principle applies when partitioning a message queue. Pick a partition key that leads to an even distribution of messages across the set of queues.
- **Partition around Azure subscription and service limits**. Individual components and services have limits, but there are also limits for subscriptions and resource groups. For very large applications, you might need to partition around those limits.
- **Partition at different levels**. Consider a database server deployed on a VM. The VM has a VHD that is backed by Azure Storage. The storage account belongs to an Azure subscription. Notice that each step in the hierarchy has limits. The database server may have a connection pool limit. VMs have CPU and network limits. Storage has IOPS limits. The subscription has limits on the number of VM cores. Generally, it's easier to partition lower in the hierarchy. Only large applications should need to partition at the subscription level.

## Design for operations

> Design your application so that the operations team has the tools they need.

Some of the important functions of the operations team include:
- [[Deployment]]
- [[Monitoring]]
- [[Escalation]]
- [[Incident]] response
- [[Security]] auditing

Robust [[logging]] and [[tracing]] are particularly important in cloud applications. Involve the operations team in design and planning, to ensure the application gives them the data and insight they need to be successful.

### Recommendations

- **Make all things observable**. Once a solution is deployed and running, logs and traces are your primary insight into the system. *Tracing* records a path through the system, and is useful to pinpoint bottlenecks, performance issues, and failure points. *Logging* captures individual events such as application state changes, errors, and exceptions
- **Instrument for monitoring**. Monitoring gives insight into how well (or poorly) an application is performing, in terms of availability, performance, and system health. It should be as close to real-time as possible, so that the operations staff can react to issues quickly. Ideally, monitoring can help avert problems before they lead to a critical failure.
- **Instrument for root cause analysis**. Root cause analysis is the process of finding the underlying cause of failures. It occurs after a failure has already happened.
- **Use distributed tracing**. Use a distributed tracing system that is designed for concurrency, asynchrony, and cloud scale. Traces should include a [[correlation ID]] that flows across service boundaries.
- **Standardize logs and metrics**. Define a common schema for log aggregation.
- **Automate management tasks**, including provisioning, deployment, and monitoring. Automating a task makes it repeatable and less prone to human errors.
- **Treat configuration as code**. Check configuration files into a version control system, so that you can track and version your changes, and roll back if needed.

## Use managed services

> When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).

[[IaaS]] offers access to computing resources, like servers, storage, and networks.

[[PaaS]] provides a framework for developing and running apps.

Advantages of PaaS over IaaS: PaaS options are easier to configure and administer; you do not need configure VMs.

### Use an identity service

> Use an identity as a service (IDaaS) platform instead of building or operating your own.

Identity is the foundation of modern security practices like [[zero trust]]. For most solutions, we strongly recommend using an [[identity as a service]] ([[IDaaS]]) platform, a fully managed identity solution, instead of building or operating your own. In this article, we describe the challenges of building or running your own identity system.

#### Avoid storing credentials

You should never store [[credential]]s in clear text, or even as encrypted data. You might consider cryptographically [[hash]]ing and [[salt]]ing credentials before storing them, which makes them more difficult to attack. However, even hashed and salted credentials are vulnerable to several types of attack.

*Consider credential storage to be a liability, not an asset*.

#### Implement identity and federation protocols

Identity federation protocols are complex to establish, manage, and maintain, and they require specialist knowledge and experience. Read more [[Federated identity]] pattern: https://docs.microsoft.com/en-us/azure/architecture/patterns/federated-identity

#### Adopt modern identity features

Users expect an identity system to have a range of advanced features, including:
- Passwordless authentication, which uses secure approaches to sign in that don't require users to enter credentials.
- [[Single sign-on]] ([[SSO]]), which allows users to sign in by using an identity from their employer, school, or another organization.
- [[Multifactor authentication]] ([[MFA]]), which prompts users to authenticate themselves in multiple ways. For example, a user might sign in by using a password and also by using an authenticator app on a mobile device or a code that's sent by email.
- Auditing, which tracks every event that happens in the identity platform, including successful, failed, and aborted sign-in attempts. To forensically analyze a sign-in attempt later might require a detailed log.
- Conditional access, which creates a risk profile around a sign-in attempt that's based on various factors. The factors might include the user's identity, the location of the sign-in attempt, previous sign-in activity, and the sensitivity of the data or application.
- Just-in-time access control, which temporarily allows users to sign in based on an approval process, and then removes the authorization automatically.

[[IDaaS]] platforms can also provide an improved set of security features that are based on the volume of sign-in requests that they receive. For example, the following features work best when there's a large number of customers who use a single identity platform:
- Detection of risky sign-in events, such as sign-in attempts from botnets
- Detection of impossible travel between a user's activities
- Detection of common credentials, such as passwords that are frequently used by other users, which are therefore subject to a heightened risk of compromise
- Use of machine learning techniques to classify sign-in attempts as valid or invalid
- Monitoring of the so-called dark web for leaked credentials and preventing their exploitation
- Ongoing monitoring of the threat landscape and the current vectors that attackers use

#### Use a reliable, high-performance identity system

An identity system must perform well and be able to scale to the level of growth that your system might experience

#### Test your security and apply tight controls

If you run an identity system, it's your responsibility to keep it secured. Examples of the controls you need to consider implementing include:
- Periodic penetration testing, which requires specialized expertise.
- Vetting of employees and anybody else with access to the system.
- Tight control of all changes to your solution with all changes reviewed by experts.

These controls are often expensive and difficult to implement.

## Use the best data store for the job

> Pick the storage technology that is the best fit for your data and how it will be used.

Relational [[SQL]] databases come with costs:
- Queries can require expensive joins.
- You need to normalize the data and restructure it for schema on write.
- Lock contention can affect performance.

### Alternatives to relational databases

- Key/value stores
- Document databases
- Search engine databases
- Time series databases
- Column family databases
- Graph databases

### Recommendations

- Don't use a relational database for everything. Consider other data stores when it's appropriate.
- Remember that data includes more than just persisted application data. It also includes application logs, events, messages, and caches.
- Embrace [[polyglot]] persistence, or solutions that use a mix of data store technologies.
- Consider the type of data that you have. For example:
  - Put transactional data into a [[SQL]] database.
  - Store [[JSON]] documents in a [[document database]].
  - Use a time series data base for [[telemetry]].
  - Put application logs into [[Azure Cognitive Search]].
  - Choose [[Azure Blob Storage]] for [[blobs]].
- Prefer availability over (strong) consistency. Base on [[CAP theorem]], a distributed system can implement only two of the three features ([[Consistency]], [[Availability]], and [[Partition Tolerance]]) at any one time.
- Consider the skill set of your development team. There are advantages to using polyglot persistence, but it's possible to go overboard. The team needs to understand how to:
  - Optimize queries.
  - Tune for performance.
  - Work with appropriate usage patterns.
- Use [[compensating transactions]]. A side effect of polyglot persistence is that a single transaction might write data to multiple stores. If something fails, use [[compensating transactions]] to undo any steps that already finished.
- Look at [[bounded contexts]], a concept from [[domain-driven design]]. A bounded context defines which parts of the domain the model applies to. Ideally, a bounded context maps to a subdomain of the business domain.

## Design for evolution

> All successful applications change over time. An evolutionary design is key for continuous innovation.

All successful applications change over time, whether to fix bugs, add new features, bring in new technologies, or make existing systems more scalable and resilient. If all the parts of an application are tightly coupled, it becomes very hard to introduce changes into the system.

[[Microservices]] are becoming a popular way to achieve an evolutionary design, because they address many of the considerations listed here.

### Recommendations

- **Enforce high cohesion and loose coupling**. A service is cohesive if it provides functionality that logically belongs together. Services are loosely coupled if you can change one service without changing the other.
- **Encapsulate domain knowledge** that falls under a specific service's resonpsibility.
- **Use asynchronous messaging**. With a pub/sub architecture, the producer may not even know who is consuming the message. New services can easily consume the messages without any modifications to the producer.
- **Don't build domain knowledge into a gateway**
- **Expose open interfaces**. Avoid creating custom translation layers that sit between services. Instead, a service should expose an API with a well-defined API contract
- **Design and test against service contracts**. When services expose well-defined APIs, you can develop and test against those APIs.
- **Abstract infrastructure away from domain logic**. Don't let domain logic get mixed up with infrastructure-related functionality, such as messaging or persistence. Otherwise, changes in the domain logic will require updates to the infrastructure layers and vice versa.
- **Offload cross-cutting concerns to a separate service**
- **Deploy services independently**

## Build for the needs of business

> Every design decision must be justified by a business requirement.

### Recommendations

- **Define business objectives**, such as [[recovery time objective]] (RTO), [[recovery point objective]] (RPO), and [[maximum tolerable outage]] (MTO). These numbers should inform decisions about the architecture
- **Document [[service level agreements]] ([[SLA]]s) and [[service level objectives]] ([[SLO]]s)**, including availability and performance metrics.
- **Model applications for your business domain**. Analyze the business requirements, and use these requirements to model the solution. Consider using a [[domain-driven design]] ([[DDD]]) approach to create domain models that reflect your business processes and use cases.
- **Define functional and nonfunctional requirements**. Functional requirements determine whether an application performs its task. Nonfunctional requirements determine how well the application performs.
- **Decompose workloads**. Workload in this context means a discrete capability or computing task that can logically be separated from other tasks. Different workloads might have different requirements for availability, scalability, data consistency, and disaster recovery.
- **Plan for growth**.
- **Manage costs**

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
14. https://docs.microsoft.com/en-us/azure/architecture/best-practices/monitoring
15. https://docs.microsoft.com/en-us/azure/architecture/patterns/federated-identity
16. https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview
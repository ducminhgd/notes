---
type: architecture
keywords:
- best practices
- architecture
- cloud
- fault
---
# Transient Fault Handling

Transient faults include the momentary loss of network connectivity to components and services, the temporary unavailability of a service, or timeouts that arise when a service is busy. These faults are often self-correcting, and if the action is repeated after a suitable delay it is likely to succeed.

## Why do transient faults occur in the cloud?

Transient faults can occur in any environment, on any platform or operating system, and in any kind of application. In solutions that run on local on-premises infrastructure, the performance and availability of the application and its components is typically maintained through expensive and often underused hardware redundancy, and components and resources are located close to each other.

Cloud hosting, including private cloud systems, can offer higher overall availability by using shared resources, redundancy, automatic failover, and dynamic resource allocation across many commodity compute nodes. However, the nature of these environments can mean that transient faults are more likely to occur. There are several reasons for this:
- Many resources in a cloud environment are shared, and access to these resources is subject to throttling in order to protect the resource.
- Cloud environments are built using vast numbers of commodity hardware units.
- There are often more hardware components, including network infrastructure such as routers and load balancers, between the application and the resources and services it uses. This additional infrastructure can occasionally introduce additional connection latency and transient connection faults.
- Network conditions between the client and the server may be variable, especially when communication crosses the Internet.

## General guidelines

- Determine if there is a built-in retry mechanism:
  - Many services provide an SDK or client library that contains a transient fault handling mechanism. The retry policy it uses is typically tailored to the nature and requirements of the target service.
  - Use the built-in retry mechanism where available, unless you have specific and well-understood requirements that make a different retry behavior more appropriate.
- Determine if the operation is suitable for retrying:
  - You should only retry operations where the faults are transient, and if there is at least some likelihood that the operation will succeed when reattempted.
  - In general, you should implement retries only where the full impact of this can be determined, and the conditions are well understood and can be validated
  - When you create services or components, consider implementing error codes and messages that will help clients determine whether they should retry failed operations
- Determine an appropriate retry count and interval:
  - It is vital to optimize the retry count and the interval to the type of use case. *This is based on experiences with your services*
  - The appropriate values for the time interval and the number of retry attempts depend on the type of operation being attempted.
  - Determining the appropriate intervals between retries is the most difficult part of designing a successful strategy. Typical strategies use the following types of retry interval:
    - **Exponential back-off**. The application waits a short time before the first retry, and then exponentially increasing time between each subsequent retry.
    - **Incremental intervals**. The application waits a short time before the first retry, and then incrementally increasing time between each subsequent retry.
    - **Regular intervals**. The application waits for the same period of time between each attempt.
    - **Immediate retry**. Sometimes a transient fault is brief, perhaps due to an event such as a network packet collision or a spike in a hardware component. In this case, retrying the operation immediately is appropriate because it may succeed if the fault has cleared in the time it takes the application to assemble and send the next request. However, there should never be more than one immediate retry attempt, and you should switch to alternative strategies, such as exponential back-off or fallback actions, if the immediate retry fails.
    - **Randomization**. Any of the retry strategies listed above may include a randomization to prevent multiple instances of the client sending subsequent retry attempts at the same time.
  - Take into account the combination of all the factors that contribute to the overall maximum timeout for a retried operation.
  - Overly aggressive retry strategies, which have intervals that are too short or retries that are too frequent, can have an adverse effect on the target resource or service. This may prevent the resource or service from recovering from its overloaded state, and it will continue to block or refuse requests.
- Avoid anti-patterns:
  - Avoid implementations that include duplicated layers of retry code.
  - Avoid designs that include cascading retry mechanisms, or that implement retry at every stage of an operation that involves a hierarchy of requests, unless you have specific requirements that demand this.
  - Never implement an endless retry mechanism.
  - Never perform an immediate retry more than once.
  - Avoid using a regular retry interval, especially when you have a large number of retry attempts.
  - Prevent multiple instances of the same client, or multiple instances of different clients, from sending retries at the same times.
- Test your retry strategy and implementation. To check behavior during testing, you can:
  - Inject transient and nontransient faults into the service.
  - Create a mock of the resource or service that returns a range of errors that the real service may return. Ensure you cover all the types of error that your retry strategy is designed to detect.
  - Force transient errors to occur by temporarily disabling or overloading the service if it is a custom service that you created and deployed.
  - Perform high load factor and concurrent tests to ensure that the retry mechanism and strategy works correctly under these conditions, and does not have an adverse effect on the operation of the client or cause cross-contamination between requests.
- Manage retry policy configurations:
  - A retry policy is a combination of all of the elements of your retry strategy. It defines the detection mechanism that determines whether a fault is likely to be transient, the type of interval to use (such as regular, exponential back-off, and randomization), the actual interval value(s), and the number of times to retry.
  - Retries must be implemented in many places within even the simplest application, and in every layer of more complex applications. Rather than hard-coding the elements of each policy at multiple locations, consider using a central point for storing all the policies.
  - Take advantage of built-in or default retry strategies available in the client APIs you use, but only where they are appropriate for your scenario.
- Log and track transient and nontransient faults:
  - As part of your retry strategy, include exception handling and other instrumentation that logs when retry attempts are made.
  - Log transient faults as Warning entries rather than Error entries so that monitoring systems do not detect them as application errors that may trigger false alerts.
  - Consider storing a value in your log entries that indicates if the retries were caused by throttling in the service, or by other types of faults such as connection failures, so that you can differentiate them during analysis of the data.
  - Consider measuring and logging the overall time taken for operations that include a retry mechanism. Also log the number of retries occurred in order to understand the factors that contributed to the response time.
  - Consider implementing a telemetry and monitoring system that can raise alerts when the number and rate of failures, the average number of retries, or the overall times taken for operations to succeed, is increasing.
- Manage operations that continually fail:
  - Although a retry strategy will define the maximum number of times that an operation should be retried, it does not prevent the application repeating the operation again, with the same number of retries.
  - To prevent continual retries for operations that continually fail, consider implementing the Circuit Breaker pattern
  - To prevent continual retries for operations that continually fail, consider implementing the Circuit Breaker pattern
  - In the meantime, it may be possible to fall back to another instance of the service (perhaps in a different datacenter or application), use a similar service that offers compatible (perhaps simpler) functionality, or perform some alternative operations in the hope that the service will become available soon.
- Other considerations
  - When deciding on the values for the number of retries and the retry intervals for a policy, consider if the operation on the service or resource is part of a long-running or multistep operation.
  - Consider if retrying the same operation may cause inconsistencies in data.
  - Consider the scope of the operations that will be retried.
  - If you choose a retry scope that encompasses several operations, take into account the total latency of all of them when determining the retry intervals, when monitoring the time taken, and before raising alerts for failures.
  - Consider how your retry strategy may affect neighbors and other tenants in a shared application, or when using shared resources and services
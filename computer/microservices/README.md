---
type: microservices
---
# Microservice architecture

*I noted by the follow by [microservices.io](https://microservices.io/), just in case the website cannot be visited anymore.*

![Microservice Pattern Language by microservice.io](https://microservices.io/i/MicroservicePatternLanguage.jpg)

## Application patterns

### Decomposition

Using [4+1 architectural view model](https://en.wikipedia.org/wiki/4%2B1_architectural_view_model) to decide how to decompose services. These notes below are from **Microservices patterns** of **Chris Richarson*.

- *Logical view* - The software elements that are created by developers. In object-oriented languages, these elements are classes and packages. The relations between them are the relationships  between classes and packages, including inheritance, associations, and depends-on.
- *Implementation view* - The output of the build system. This view consists of modules, which represent packaged code, and components, which are executable or deployable units consisting of one or more modules. In Java, a module is a JAR file, and a component is typically a WAR file or an executable JAR file. The relations between them include dependency relationships between modules and composition relationships between components and modules.
- *Process view* - The components at runtime. Each element is a process, and the relations between processes represent interprocess communication.
- *Deployment* - How the processes are mapped to machines. The elements in this view consist of (physical or virtual) machines and the processes. The relations between machines represent networking. This view also describes the relationship between processes and machines.

- [[Decompose by business capability]] [read more](decompose-by-business-capability.md)
- [[Decompose by subdomain]] [read more](decompose-by-subdomain.md)
- [[Self-contained Service]] [read more](self-contained-service.md)
- [[Service per team]] [read more](service-per-team.md)

### Data patterns

#### Database architecture

- [[Shared database]] [read more](shared-database.md)
- [[Database per Service]] [read more](database-per-service.md)

#### Querying

- [[API Composition]] [read more](api-composition.md)
- [[CQRS]] [read more](cqrs.md)

#### Maintaining data consistency

- [[Aggregate]] [read more](aggregate.md))
- [[Saga]] [read more](Saga.md)
- [[Event sourcing]] [read more](event-sourcing.md)
- [[Domain event]] [read more](domain-event.md)

### Testing

- [[Consumer-driven contract test]] [read more](consumer-driven-contract-test.md)
- [[Consumer-side contract test]] [read more](consumer-side-contract-test.md)
- [[Service Component test]] [read more](service-component-test.md)

### UI

- [[Server-side page fragment composition]] [read more](server-side-page-fragment-composition.md)
- [[Client-side UI composition]] [read more](client-side-ui-composition.md)

### Observability (part 1)

- [[Audit logging]] [read more](audit-logging.md)
- [[Application metrics]] [read more](application-metrics.md)

## Application Infrastructure patterns

### Cross-cutting concerns

- [[Service template]] [read more](service-template.md)
- [[Microservice Chassis]] [read more](microservice-chassis.md)
- [[Externalized configuration]] [read more](externalized-configuration.md)

### Security

- [[Access token]] [read more](access-token.md)

### Communication patterns (part 1)

#### Transactional messaging

- [[Transactional outbox]] [read more](transactional-outbox.md)
- [[Transaction log tailling]] [read more](transactional-log-tailling.md)
- [[Polling publisher]] [read more](polling-publisher.md)

#### Communication style

- Messaging
- Remote Procedure invocation
- Domain-specific

#### Reliability

- Circuit Breaker

#### Discovery (part 1)

- Client-side discovery
- Self registration

### Observability (part 2)

- Distributed tracing
- Health check API
- Exception tracking
- Log aggregation
- Log deployments and changes

## Infrastructure patterns

### Deployment

- Multiple Services per host
- Single Service per host
- Serverless deployment
- Service-per-Container
- Service-per-VM
- Service deployment platform
- Service mesh
- Sidecar

### Communication patterns (part 2)

#### Discovery (part 2)

- Service registry
- Server-side discovery
- 3rd party registration

#### External API

- API gateway
- Backends for Frontends
---
type: microservices
---
# Microservice architecture

![Microservice Pattern Language by microservice.io](https://microservices.io/i/MicroservicePatternLanguage.jpg)

## Application patterns

### Decomposition

Using [4+1 architectural view model](https://en.wikipedia.org/wiki/4%2B1_architectural_view_model) to decide how to decompose services. These notes below are from **Microservices patterns** of **Chris Richarson*.

- *Logical view* - The software elements that are created by developers. In object-oriented languages, these elements are classes and packages. The relations between them are the relationships  between classes and packages, including inheritance, associations, and depends-on.
- *Implementation view* - The output of the build system. This view consists of modules, which represent packaged code, and components, which are executable or deployable units consisting of one or more modules. In Java, a module is a JAR file, and a component is typically a WAR file or an executable JAR file. The relations between them include dependency relationships between modules and composition relationships between components and modules.
- *Process view* - The components at runtime. Each element is a process, and the relations between processes represent interprocess communication.
- *Deployment* - How the processes are mapped to machines. The elements in this view consist of (physical or virtual) machines and the processes. The relations between machines represent networking. This view also describes the relationship between processes and machines.

- [[Decompose by business capability]]
- [[Decompose by subdomain]]
- [[Self-contained Service]]
- [[Service per team]]

### Data patterns

#### Database architecture

- Shared database
- Database per Service

#### Querying

- API Composition
- CQRS

#### Maintaining data consistency

- Aggregate
- Saga
- Event sourcing
- Domain event

### Testing

- Consumer-driven contract test
- Consumer-side contract test
- Service Component test

### UI

- Server-side page fragment composition
- Client-side UI composition

### Observability (part 1)

- Audit logging
- Application metrics

## Application Infrastructure patterns

### Cross-cutting concerns

- Service template
- Microservice Chassis
- Externalized configuration

### Security

- Access token

### Communication patterns (part 1)

#### Transactional messaging

- Transactional outbox
- Transaction log tailling
- Polling publisher

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
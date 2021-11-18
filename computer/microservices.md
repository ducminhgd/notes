# Microservices

![Microservice Pattern Language by microservice.io](https://microservices.io/i/MicroservicePatternLanguage.jpg)

## Application patterns

### Decomposition

- Decompose by business capability
- Decompose by subdomain
- Self-contained Service
- Service per team

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
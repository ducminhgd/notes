---
type: microservices
layer: Application Infrastructure patterns
group: Cross-cutting concerns
---
# Microservice Chassis

## Context

When you start the development of an application you often spend a significant amount of time writing the build logic and putting in place the mechanisms to handle cross-cutting concerns. The build logic builds, and tests the application and also packages into a production-ready format, such as a Docker container image. For example, the majority of the build logic for a Java application is implemented using Gradle or Maven. It might also consist of configuration scripts for build automation tool, such as CircleCI or Github Actions. On the surface, the build logic appears to be quite simple. However, even defining a carefully curated set of dependencies can be remarkably challenging.

In addition to the build logic, you need to handle cross cutting concerns. Examples of cross-cutting concern include:
- Security - for example, REST APIs must be secured by requiring an [[Access Token]]
- [[Externalized configuration]] - includes credentials, and network locations of external services such as databases and message brokers
- [[Logging]] - configuring of a logging framework such as log4j or logback
- [[Health check]] - a url that a monitoring service can "ping" to determine the health of the application
- [[Metrics]] - measurements that provide insight into what the application is doing and how it is performing
- [[Distributed tracing]] - instrument services with code that assigns each external request an unique identifier that is passed between services.

As well as these generic cross-cutting concerns, there are also cross-cutting concerns that are specific to the technologies that an application uses. Applications that use infrastructure services such as databases or a message brokers require boilerplate configuration in order to do that. For example, applications that use a relational database must be configured with a connection pool. Web applications that process HTTP requests also need boilerplate configuration.

It is common to spend one or two days, sometimes even longer, writing the build logic and implementing cross-cutting concerns. If you going to spend months or years developing a monolithic application then the upfront investment is insignificant. The situation is very different, however, if you are developing an application that has the [[microservice architecture]]. There are tens or hundreds of services. You will frequently create new services, each of which will only take days or weeks to develop. You cannot afford to spend a few days setting up every service. What is even worse is that in a [[microservice architecture]] there are additional cross-cutting concerns that you have to deal with including service registration and discovery, and circuit breakers for reliably handling partial failure.

One solution is to create a [[Service Template]], which is a source code template that a developer can copy in order to quickly start developing a new service. The drawback, however, of a [[Service Template]] is that it's a form of copy/paste programming: when the build logic and cross-cutting concerns logic needs to change, each service must be updated individually.

![](Service_template_limitations.png)

## Forces

- A service must implement
  - Build logic that builds, and tests the application and also packages into a production-ready format, such as a Docker container image.
  - Cross-cutting concerns such as externalized configuration, logging, health checks, metrics, service registration and discovery, circuit breakers. There are also cross-cutting concerns that are specific to the technologies that the microservices uses.
- Creating a new microservice should be fast and easy
- It should be fast and straightforward to update existing services when the requirements for build logic and cross-cutting concerns change.

## Solution

Create a microservice chassis framework that can be foundation for developing your microservices. The chassis implements
- Reusable build logic that builds, and tests a service. This includes, for example, Gradle Plugins.
- Mechanisms that handle cross-cutting concerns. The chassis typically assembles and configures a collection of frameworks and libraries that implement this functionality.

The [[Service Template]] is a sample service that uses the microservice chassis.

![](Microservice_chassis.png)

## Example

Examples of frameworks that are the starting point for creating a microservice chassis:
- Java
  - [[Spring Boot]]  and Spring Cloud
  - Dropwizard
- Go
  - Gizmo
  - Micro
  - Go kit

## Resulting context

The major benefit of a microservice chassis is that you can quickly and easy get started with developing a microservice.

One issue is that you need a microservice chassis for each programming language/framework that you want to use. This can be an obstacle to adopting a new programming language or framework.

## Related patterns

There are the following related patterns:
- [[Service Template]] - a template for a service that developers can copy to create a new application. It's either an alternative to the Microservice Chassis pattern; or it uses a Microservice Chassis and simply contains the code and configuration that doesn't belong in the chassis
- [[Microservices]] - this pattern motivates the need for the Microservice Chassis pattern
- [[Self Registration]] - the microservice chassis is often responsible for registering the service with the service registry
- [[Client-side discovery]] - the microservice chassis is often responsible for client-side service discovery
- [[Circuit Breaker]] - the microservice chassis framework might implement this pattern
- [[Distributed tracing]] - the microservice chassis framework might instrument the code
- [[Service Template]] often uses a Microservice Chassis
---
type: microservices
layer: Application Infrastructure patterns
group: Communication patterns
subgroup: Communication style
---
# Domain-specific

## Context

You have applied the [[Microservice architecture]] pattern. Services must handle requests from the application's clients. Furthermore, services must sometimes collaborate to handle those requests. They must use an inter-process communication protocol.

## Forces

## Solution

Use a domain-specific protocol for inter-service communication.

## Examples

There are numerous domain-specific protocols including:
- Email protocols such as [[SMTP]] and [[IMAP]]
- Media streaming protocols such as [[RTMP]], [[HLS]], and [[HDS]]

## Resulting context

## Related patterns

- The [[Messaging pattern]] is an alternative pattern
- The [[RPI]] pattern is an alternative pattern
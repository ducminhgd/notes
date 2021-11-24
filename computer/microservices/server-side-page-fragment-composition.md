---
type: microservices
layer: Application patterns
group: UI
---
# Server-side page fragment composition

## Context

You have applied the [[Microservice architecture]] pattern. Services are developed by [business capability](https://microservices.io/patterns/decomposition/decompose-by-business-capability.html)/[subdomain](https://microservices.io/patterns/decomposition/decompose-by-subdomain.html)-oriented teams that are also responsible for the user experience. Some UI screens/pages display data from multiple service. Consider, for example, an Amazon-style product detail page, which displays numerous data items including:
- Basic information about the book such as title, author, price, etc.
- Your purchase history for the book
- Availability
- Buying options
- Other items that are frequently bought with this book
- Other items bought by customers who bought this book
- Customer reviews
- Sellers ranking
- …

Each data item corresponds to a separate service and how it is displayed is, therefore, the responsibility of a different team.

## Problem

How to implement a UI screen or page that displays data from multiple services?

## Forces

## Solution

Each team developers a web application that generates the HTML fragment that implements the region of the page for their service. A UI team is responsible for developing the page templates that build pages by performing server-side aggregation (e.g. server-side include style mechanism) of the service-specific HTML fragments.

## Related patterns

The [[Client-side UI composition]] pattern is an alternative approach
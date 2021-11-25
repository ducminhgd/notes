---
type: microservices
layer: Application patterns
group: UI
---
# Client-side UI composition

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

Each team develops a client-side UI component, such an AngularJS directive, that implements the region of the page/screen for their service. A UI team is responsible implementing the page skeletons that build pages/screens by composing multiple, service-specific UI components.

## Related patterns

The [[Server-side page fragment composition]] pattern is an alternative approach


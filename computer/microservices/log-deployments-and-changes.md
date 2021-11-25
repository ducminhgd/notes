---
type: microservices
layer: Application Infrastructure patterns
group: Observability
---
# Log deployments and changes

## Context

You have applied the [[Microservice architecture]] pattern.

## Problem

How to understand the behavior of an application and troubleshoot problems?

## Forces

It useful to see when deployments and other changes occur since issues usually occur immediately after a change

## Solution

Log every deployment and every change to the (production) environment.

## Examples

A deployment tool can, for example, publish a [pseudo-metric](application-metrics.md) whenever it deploys a new version of a service. This metric can then be displayed alongside other metrics enabling changes in application behavior to be correlated with deployments. See [Tracking Every Release by Mike Brittain](https://codeascraft.com/2010/12/08/track-every-release/)

AWS Cloud Trail provides logs of AWS API calls.

## Resulting Context

This pattern has the following benefits:
- Enables deployments and changes to be easily correlated with issues leading to faster resolution.

## Related patterns
---
title: "Microservice Communication"
date: 2022-12-10T09:52:01+08:00
tags: []
categories: ["Microservice"]
summary: "Implementing Microservice Communication"
---

## Ideal Technology

* Make Backward Compatibility Easy.
* Allow Different Client Technology Stack.

## Technology Choices

### RPC

#### Network

Performance, Failure, Security.

#### Brittleness

Objects used as part of binary serialization across the wire can be thought of as **expand-only** types.

#### Notice

Don’t abstract your remote calls to the point that the network is completely hidden.

Ensure that you can evolve the server interface without breaking any client.

#### REST

##### Level 1

Rather than making all requests to a singular service endpoint, we now start talking to individual resources.

##### Level 2

* HTTP Verbs: safe (GET) and not-safe operations.
* Status Codes: help communicate the kinds of errors you run into.

##### Level 3

Hypermedia Controls: tell us what we can do next and the URI to do it.

#### GraphQL

#### Message Brokers

---
layout: post
title:  "J4K-2019: Ivar Grimstad: Microprofile"
date:   2019-07-30 16:20 -0400
comments: false
---

Raw Notes from Ivar Grimstad's session.

![Ivar Grimstad]({{ site.url }}/blog/assets/20190730-ivar-grimstad.jpg "Ivar Grimstad")

## Microprofile 

[microprofile.io](microprofile.io)

3.0 now available

## Microservice patterns

[microservices.io](microservices.io) Chris Richardson

## Configuration

The Config spec.

* System Properties

* Env Vars

* Application Configuration

```
META-INF/microprofile-config.properties
```

* Demoed with Helidon.

## Healthcheck

MicroProfile Healthcheck API

Demoed with Thorntail.

* Did explicitily mention there are two healthchecks: 1. I'm
  alive (liveness) 2. I'm ready to receive requests. (Readiness)
  
* Automatically adds `/health` endpoint.

* Demoed `@Liveness` and `@Readiness`.

## Metrics

MicroProfile Metrics

## Access Token

* Basic Auth

* OAuth 2.0

## Distributed Tracing

* Zipkin

* Jaeger

OpenTracing 1.3

## Everything Together

Three microservices

* Capitalize

* Birthday

* Hello

Nice example because it shows circuit breaker, RBAC, 

`@RegisterRestClient`
`@Retry`
`@RolesAllowed`

[jwt.io](jwt.io)

This talk was really awesome.  The fact that everything he demoed was
available without any fancy service mesh, load balancer, container
orchestration, is so nice and understandable.  I like how he mapped all
the microservices patterns to the microprofile specs.

![Resources]({{ site.url }}/blog/assets/20190730-microprofile-resources.jpg "Resources")

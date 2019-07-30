---
layout: post
title:  "J4K-2019: Paul Parkinson: Helidon and Kubernetes"
date:   2019-07-30 11:00 -0400
comments: false
---

Raw Notes from Paul Parkinson's session.

![Paul Parkinson]({{ site.url }}/blog/assets/20190730-paul-parkinson.jpg "Paul Parkinson")

## Workflow of Getting into Kubernetes

* Service Broker, Kubernetes Secret, Microservice

* `CloudEvents` standard

* ServiceMesh, Istio, Envoy Data Plane

* Side note: Tracing has become easier.  `Kiali`.  Uses opentracing,
  Jaeger, Zipkin.
  
   * Gave some background on Jaeger.  

* WebLogic Kubernetes Operator

* Helidon SE

   * Microframework
   
   * Functional
   
   * Reactive
   
   * Tracing
   
   * No annotations
   
   * Messaging client

* Helidon MP

   * Full Microprofile standard
   
   * Familiar to Java EE Devs
   
   * Tracing
   
   * JTA/JPA
   
   * Oracle DB feature integration

   * Long Running Activities
   
## Long Running Activities

   * Annotations `@LRA`.  These are the newer version of JTA
   
   * Ties in to DDD Bounded Contexts.  When decomposing the monolith and
     the DB in particular, this is where migration efforts fail.  Close
     attention needs to be paid.
     
## Microservices Data Management Patterns

* CQRS

* Access thru public APIs onlt

* Database per service

## Microservices Communication

* No locking

* Event based integration

   * Pub/sub
   
   * Event Sourcing
   
      * Gave basic definition, illustrated how it works.
      
   * CQRS
    
      * Same story, gave definition and illustrated how it works.
      
   * Atomicity of Persisting Events and Database States
   
      * Oracle Advanced Queue: A messing system in the database.  16
        year old technology, but fits really well with Microservices.
        Guaranteed once delivery.  Idempotency for free.
        
   * SAGA Pattern
   
      * A saga is a sequence of local transactions.  Two
        flavors. Choreography and Orchestration.  The latter is you have
        an orchestrator that controls the actions of the participants.
        
      * Compensating transactions.  Showed some code slides of
        complexity with and without AQ.
        
## Demo




---
layout: post
title:  "Oracle Code One 2019: Reza Rahman: Jakarta EE Recepies"
date:   2019-09-19 13:15 -0700
comments: false
---

Raw notes from @reza_rahman Jakarta EE Recepies.

![Reza]({{ site.url }}/blog/assets/20190919-reza.jpg "Reza")

## Servlet 4

* Value add: HTTP/2

## JSON Binding

* JSON is now a first class citizen of the platform.  Provides built-in
  way to support "application/json" for POJOs.
  
## JSON Pointer JSON Patch

* This one is really valuable.

![Patch]({{ site.url }}/blog/assets/20190919-reza-json-patch.jpg "Patch")

## Server Sent Events

* Another important part of HTML5 in EE.  Along with WebSockets.

## Jakarta Security

* Addresses a long festering problem: Security was never addressed
  cohesively.  As a result it was always vendor specific.
  
* Took a cross cutting look at all the specs and how they did security.
  Unified it as well as possible.
  
* Introduced a `SecurityContext` concept.

## CDI 2

* Allows it to be used from Java SE, outside of EE. **This is vital for
  allowing CDI to be used from Microprofile**.
  
* Portable SPI simplifications and enhancements.

## Adopting Java SE 8

![se8]({{ site.url }}/blog/assets/20190919-reza-se8-ee8.jpg "se8")


## Impls

[https://jakarta.ee/compatibility](https://jakarta.ee/compatibility)

* Eclipse GlassFish

* WildFly

* Open Liberty

## Microprofile

[https://microprofile.io/](https://microprofile.io/)

* Jakarta EE 8 covers most of what you need in monolithic applications

* Microprofile has kept Server Side Java alive and relevant while the
  hard, no technical Jakarta EE 8 work happened.

## Community Questions

![questions]({{ site.url }}/blog/assets/20190919-reza-questions.jpg "questions")

[http://bit.ly/2mhatfl](http://bit.ly/2mhatfl)

## JakartaEE.next

* Jakarta NoSQL

* Deprecating EJB

* OAuth2, OpenID Connect, JWT

* ManagedServiceExecutorServiceDefinition

* Java SE alignment

* Jakarta Concurrency

* MVC

Other ideas.


---
layout: post
title:  "How well did Jakarta EE 11 respond to the needs of developers?"
date:   2024-09-23 12:15-0800
comments: true
---

Cross posted at [dev.to](https://dev.to/edburns/how-well-did-jakarta-ee-11-respond-to-the-needs-of-developers-1824).

### Executive Summary

The [Jakarta Steering Committee](https://jakarta.ee/committees/steering/) chartered the [Jakarta EE Platform project](https://jakartaee.github.io/platform/) with the goal of incorporating developer feedback in the development of EE 11. This blog post reviews the performance of the Platform Project and awards a [GPA](https://gpacalculator.io/how-to-calculate-gpa/) of 3.43 on a 4 point scale of achieving this goal. 

### Introduction

I am humbled and honored to find myself in a position of helping to deliver the next iteration of Jakarta EE. I've held many roles in J2EE/Java EE/Jakarta EE over the decades: implementer, spec lead, advocate, author, tester, and more. My current role, however, is a new one for me release co-coordinator.

In this role, I co-lead (along with Arjan Tijms) the Jakarta EE Platform project, which is responsible for delivering the finished Jakarta EE specification (and component specifications), the corresponding TCK, and at least on ratifying compatible implementation for all of the specifications. Importantly, there need not be one single monolithic implementation that satisfies all the component TCKs at the same time, but there must be one single monolithic implementation that passes the Platform TCK.

In the spirit of transparency that I was fortunate enough to start over [two decades ago](https://www.ridingthecrest.com/blog/2004/06/28/welcome-javaserver-tm-faces-implementation-project.html), this blog post examines how well the [Jakarta EE Platform project](https://jakartaee.github.io/platform/) did during EE 11 in meeting one of the goals set for the Platform project by the [Steering Committee](https://jakarta.ee/committees/steering/): incorporate developer feedback.

### Underpromise and overdeliver

Institutional memory is the way groups of humans learn from mistakes and avoid repeating them. By that definition, I hope we can all agree that institutional memory is important and worth preserving. Because software is [executable knowledge](https://www.routledge.com/The-Laws-of-Software-Process-A-New-Model-for-the-Production-and-Management-of-Software/Armour/p/book/9780849314896), a very long running open-source software project is a special kind of institutional memory. A project that is a long running ecosystem of long running open-source projects is pretty much the pinnacle of special. With all that specialness in mind, what does it mean to incorporate developer feedback?

It is far easier to show responsiveness to developer feedback when the possible costs of a committing a mistake are contained within a single project. In light of the high possible costs, the Jakarta EE 11 platform project was intentionally modest with our goals for incorporating developer feedback. This is our implementation of the tried and true strategy of "underpromise and overdeliver".

Leading up to Jakarta EE 11, we conducted an open community discussion on requirements for Jakarta EE 11 and captured them in this [Jakarta EE 11 Discussion](https://docs.google.com/document/d/1m-dkvbL0iFFzitO4vt1SVq6GGSJyFdCDM2NU_FzGS10/edit#heading=h.il1isyaqw4eb) document. Let’s review the community input we received, which was primarily developer-driven, and see how we did in EE11.


### Underpromise

* Jakarta Data

* Jakarta NoSQL

* Adopt Java SE 11, 17, 21 new features and Breaking Changes

* Virtual Threads

* TCK Refactoring

* CDI Centric

   * CDI replacing managed beans
   
   * CDI repleacing EJB
   
* Resolve redundant HTTP stacks: Servlet and REST

* MicroProfile and Jakarta Alignment

* CORS support

* Jakarta Config

* Make it easier to migrate from one vendor to another

### Mixed delivery

I'm going to group the delivery in four buckets: over-delivered, delivered, somewhat delivered, did not deliver.

#### Over-delivered

* Jakarta Persistence - programmatic configuration instead of persistence.xml and a lot more [Gavin King blog post](https://gavinking.substack.com/p/a-summary-of-jakarta-persistence)
* Jakarta Security - dynamically choose an authentication mechanism [security-311](https://github.com/jakartaee/security/issues/311)

#### Delivered

* Jakarta Data

   * Yes, this new specification is present in the platform.

* Adopt Java SE 11, 17, 21 new features and Breaking Changes.

   * Yes, there are numerous specs that take advantage of new language features from 11 - 21.

* TCK Refactoring (we will deliver this. We're holding the release for it).

   * The Jakarta EE Platform TCK is a vital software component for delivering the value proposition of IT investment stability on the scale of decades. The software of the TCK has been accruing technical debt due to lack of maintenance investment. In Jakarta EE 11 we are bringing the TCK up to date with the state of the art of testing tools. This investment will enable better compatibility testing and lower the barrier to adding more tests as the Jakarta EE platform evolves.

* API Flexibility, i.e. no more Umbrella JARs.
   - No more questions like "do I have to wait for Jakarta EE xx" to have this feature?
   - Jakarta EE Platform APIs are now just a collection of default APIs.
   - Individual specs can be excluded or upgraded by the users as they wish,
   - New specs can be added as well.
   - This makes Jakarta EE platform as flexible as Spring Boot, but without having the implementation baggage in your application, the best of both worlds!

#### Somewhat delivered

* Virtual Threads

   - The concurrency specification has rigorously specified annotation attribute that requires implementations to take advantage of virtual threads if it is available in the runtime. If you're running on Java 21 or higher, you get virtual threads when you use the annotation attribute. If you're running on 17, you don't.

* CDI Centric

   * CDI replacing managed beans.
   
      * We did 
      
         * remove the `@ManagedBean` annotation.
      
         * Move the "integration" parts of CDI from the CDI spec to the platform spec.
         
         * Jakarta Concurrency adds scheduling to the `@Asynchronous` annotation to replace the `@Scheduled` annotation on EJBs [concurrency-271](https://github.com/jakartaee/concurrency/pull/271)
         * Injecting Concurrency resources into CDI beans instead of using `@Resource` in an EJB [concurrency-348](https://github.com/jakartaee/concurrency/pull/348).
         
         * Removed managed beans support in Jakarta REST.
         
         * Qualifiers for persistence units in Persistence - allow injecting persistence context in a CDI-idiomatic way.
      
* New Java features

   * records as embeddables and IDs in Jakarta Persistence.
   
   * records in Expression Language.
   
   * records in Validation (formerly Bean Validation) [validation-275](https://github.com/jakartaee/validation-spec/pull/275).
   
   * Flow API in Concurrency [concurreny-368](https://github.com/jakartaee/concurrency/pull/368).

      
* MicroProfile and Jakarta Alignment

   * We did

      * Create the Jakarta Security MicroProfile Security bridge spec.
      
#### Did not deliver

* Jakarta NoSQL

   - This did not pass the ballot at the beginning of the EE 11 development cycle. In my opinion, the reasons were non-technical, and therefore can be resolved for EE 12.

* Resolve redundant HTTP stacks: Servlet and REST

   - This is a very big one. In my opinion, it would take a major vendor standing behind this idea and dedicating significant resources to make it happen, likely with donating work to competitors so they could also do the same.

* CORS support

   - This one did not even emerge on my radar.

* Jakarta Config

   - This one seems to be stuck in a "MicroProfile Config is good enough", and thus is falling between the cracks. I think we'd have to convince the MicroProfile project to allow this to move from MicroProfile to the Jakarta EE Core Profile specification.

* Make it easier to migrate from one vendor to another

   - This one is antithetical to the business interests of each vendor, so I don't see this one getting much attention.
   
### Summary

Let's get quantitative. For each item in the **Underpromise** list, I'll give us a letter grade. A for over-delivered or delivered, B for somewhat delivered, D for did not deliver.

| Feedback to incorporate | Grade |
|=========================|=======|
| Jakarta Data | A |
| Jakarta NoSQL | D |
| Adopt Java SE 11, 17, 21 new features and Breaking Changes | A  |
| Virtual Threads | A |
| TCK Refactoring | A |
| CDI Centric | A |
| Resolve redundant HTTP stacks: Servlet and REST | D |
| MicroProfile and Jakarta Alignment | B |
| CORS support | D |
| Jakarta Config | D |
| Make it easier to migrate from one vendor to another | D |

With this list, we only scored a 2.54 GPA. Not so great. If we strike from the list the developer feedback requests that I judge are not realistic to include (CORS, Redundant HTTP stacks, Jakarta Config, Make it easier to migrate from one vendor to another), we get a better grade: 3.43. Not bad, but we have room to grow.



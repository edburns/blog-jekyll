---
layout: post
title:  "J4K-2019: Adam Bien: Jakarta EE - Microprofile - Quarkus"
date:   2019-07-30 10:00 -0400
comments: false
---

Raw Notes from Adam Bien's session.

![Adam Bien]({{ site.url }}/blog/assets/20190730-adam-bien.jpg "Adam Bien")

# Local

* Ran archetype for simple Jakarta EE.

   * Stated, "no-one cares about distinction between Jakarta EE and
     Microprofile, they just use what's there."
     
   * "Time to first commit": needs to be < 30 seconds.
   
   * Showed dependencies.  2: EE8 Jar, and Microprofile Jar
   
* Started WebSphere OpenLiberty

* Made a change to the rest endpoint, saw that the app was updated.

   * Demonstrates that the ThinWar needs to be less than 3kB.
   
   * His little script rebuilt and deployed it.
   
# Local Cloud

* OpenShift, with minishift

* Then scaled with `oc scale dc j4k`

* Then added Prometheus metrics, with `@Metered` annotation on the REST
  endpoint.
  
* Drive home again the "start with everytning (Jakarta EE) and strip it
  down as needed" idea
  
* Added metrics with `microprofile.metrics.MetricRegistry`.

* Side story: Hystrix for circuit breaker.  Netflix pulled support.
  However, Microprofile had it, so it was ok.
  
* Skipped the actual impl of metrics.  Went to Quarkus.

# Quarkus

* Ran quarkus maven archetype

* Ran quarkus runner.  Examined what was in the runner.  The `lib`
  directory is like the base docker image.  The business logic is what
  changes and small.
  
* Hit the "one war one server" message.  No need for hot deploy.  Just
  re-start the server.
  
* He thinks Quarkus takes the app server one step further because of its
  agility: Quarkus EE.  
  
   * Added CDI.  Added Metrics.
   
# Docker Native

* Create a native Docker image on your machine.  This is comporable to `go`. 



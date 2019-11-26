---
layout: post
title:  "JVM-CON 2019 Anatole Tresch: How Not To Build An App"
date:   2019-11-25 10:30+0100
comments: false
---

Raw Notes from Anatole Tresch's session

![Anatole Tresch]({{ site.url }}/blog/assets/20191125-anatole.jpg "Anatole Tresch")

The talk took several aspects of building enterprise software and for
each aspect Anatole shared some bad things, aka anti-patterns from his
experience.  It was delivered as a "question/answer" framework.  The
questions resonated with developers.

## Management

* Unrealstic deployment expectations, that ignore and even insult the
  engineers working on the deployment.
  
* Q: `Can't we just build a private cluster in the public cloud`?

* Q: `Where's Benni today`: Not on this project any more.

   * Not good management of human responsibilities.
   
## The Human Factor

* Good employees are...

* Good managers are...

### Try not to laugh challenge...

Building on the meme from YouTube.

Some actual code examples.

## The System

* Listed the runtime SLA requirements for a system.

### Architecture

* modern Browser based

* Java

* Performance better than fat client system

* Functions as a blueprint for other applications

### Architecture decisions

* The decisions made during the development.

### Reality check

* Some problems encountered

   * Spring Boot likes Tomcat standalone, does not behave exactly the same when in JBoss
   
   * Application monitoring doesn't work that way
   
   * Redundant and incompatible security mechanisms
   
   * One monolithic build for whole project
   
      * Can't separate the generalized artifacts and the original application
      
   * Stateless
   
      * Caused high complexity in the UI
      
      * Duplication of backend logic on the business-tier.  For example: value objects.
      
   * Choice to use subversion
   
      * Quality and stability problem
      
      * Huge merge problems
      
      * Ended up building the git workflow anyway.
      
   * Database
   
      * Database version not under control of project team
      
      * Problems with unrelated runtime changes
      
   * Backend
   
      * Complex OSGi based installation
      
      * Not scalable 5 parallel users on one instance.  Too expensive.
      
      * Took a couple weeks to get the back end updated to a new version
      
      * ![backend]({{ site.url }}/blog/assets/20191125-anatole-01.jpg "backend")
      
      

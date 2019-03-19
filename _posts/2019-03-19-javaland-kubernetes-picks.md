---
layout: post
title:  "Raw Notes: Master your Java Applications in Kubernetes"
date:   2019-03-19 11:45 +0100
comments: true
---

* CV Slide.  History from consulting, banks, insurance, on-prem

   * Cloud is not his history, but now he's all about it.
   
* Agenda

   * Choose your Java runtime
   
   * Build and execute your app
   
   * Create your image
   
   * Run in K8s
   
   * Final thoughts
   
* How to decide to go with k8s

   * Top down or bottom up?
   
   * How to integreate?
   
   * Slow startup: naieve impl of monolith decomposition.  Take EE app,
     put it in container, run on k8s.
     
   * K8s is for horizontal scaling, not vertical scaling
   
   * You can horizontally scale infinitely, until the first bill comes
     in!
     
   * More hands working with K8s
   
* Choosing the Java runtime

   * Support
   
   * LTS
   
   * Container aware?
   
      * Since 8u131 & JDK 9
      
      * Changes in 8u191 & JDK10
      
      * CommentL what does he mean with container aware?  
      
* Many options out there.

   * OpenJ9 is the IBM JDK
   
      * Contributed to Eclipse in 2017
      
      * Take OpenJDK, replace Hotspot
      
      * Small memory footprint and fast startup.
      
   * GraalVM
   
      * Universal VM for running various langauges
      
      * Removes isolation and enables interop
      
   * How is this related to K8s
   
      * JVM needs to be aware of containers CPU and memory
      
      * Keep the memory and size footprint small
      
      * Keep it with fast startup time.  You can burn money for no good.
      
   * "Basic" container specific flags
   
![Flags 01]({{
   site.url }}/blog/assets/k8s-runtime-flags-01.jpg "Flags 01")

![Flags 02]({{
   site.url }}/blog/assets/k8s-runtime-flags-02.jpg "Flags 02")

  * Ahead of time compilation (AOT)
  
  * Application Class Data Sharing
  
  * Since JDK10 (JEP310)
  
  * Flag *UseAppCDS* (automatically enabled in JDK12)
  
  * Reduce memory footprint startup time
  
  * Needs two prep steps
  
      * DEMO
      
         * Start a vertx app
         
         * Create AppCDS, `class.lst`, but does not include all the classes.
         
         * Create archive file from `class.lst`.
         
         * COMMENT: this is really great.  Explore this.
         
* CDS and AOT in OpenJ9

   * enable class data sharing and AOT is enabled by default
   
   * Looks like it does the preceding for you automatically.
   
* Native compilation

   * Based on Graal compiler
   
      * Download GraaLVM
      
      * Build fat jar
      
      * native-image -jar app.jar && ./app
      
   * Works with Micronaut, Spark Java, Vert.X.
   
   * Not there yet.
   
* Benchmarks: Always do your own

* Create your images

  * Shrink your images to a minimum
  
     * FROM openjdk:12-ea-jdk-alpine3.8: as builder
     
     * jlink --add-modules ... --output /opt/jre-minimal
     
     * COPY --from=builder /opt/jre-minimal /opt-jre-minimal
     
   * Create lean images
   
     * put most changeable stuff lowest at bottom of Dockerfile
     
     * FROM adoptopenjdk/openjdk11-openj9:alpine-slim
     
   * Think about your images
   
      * Try to avoid "fat" jars and wars.
      
* Choosing your top level FROM image

   * Look for vendor-specific images 
   
   * Don't use :latest, or no tag.
   
* DEMO: Graal Native

   * A bunch of `docker run`.  Then `docker stats`.
   
* Run the app in K8s.

   * Share your CDS files in K8s
   
   * Set resource request limits.  PodSpec: `resources.requests.{memory,cpu}
     resources.requests.limits.{memory,cpu}`
     
   * Apply quotas on Namepsace level.  `kind:ResourceQuota`
   
   * ConfigMaps & Secrets to externalize your config
   
* Final thoughts

   * Avoid infrastructure code in your applications
   
      * Service mesh with circuit breaker
      
      * Service discovery using DNS or Labels in K8s
      
      * Configuration via ConfigMap
      
   * Helm charts to ship your application
   
      * Package manager like "apt" in Debian
      
      * Kubernetes Operators helps you manage upgrades, lifecycle, and
        insights.
        
   * Don't overlook serverless
   
      * Better utilization of your cluster: accomodate different workloads
      
      * Knative, an operator from Google and Pivotal
      
   * GraalVM and other projects focusing on low footprint & fast startup
   
   * Why optimize?  COSTS.  Smaller footprint, CPU, cycles, pay per use.
   
      * lifecycle has changed.  Startup is now critical.

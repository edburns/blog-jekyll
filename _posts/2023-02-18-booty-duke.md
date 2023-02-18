---
layout: post
title:  Notes from watching Ivar Grimstad Devoxx Belgium 2022 Jakarta EE 10 presentation
date:   2023-02-18 18:10-0400
comments: true
---

50 minutes https://www.youtube.com/watch?v=8ke0kPrg9qc

Simplicity for Modern and Lightweight Cloud Applications

- Out on 2022-09-22.

   - Community driven, modernized, simplified, lightweight.
   
- Specs and Impls

   - Spec, API, TCK
   
   - Compatibile Impls
   
   - We need at least one open source impl.
   
   - Other impls can be fine, not open source.
   
   - There is no "official" "reference implementation"
   
- Lots of specs, more than half received updates in EE 10

  - About half of these have a ".0" version number. In our impl of
    SemVer, the .0 means it may have potentially breaking changes.
    
    Also a bit of marketing to signal there are big noteworthy
    changes. (I like this idea)

  - Authorization 2.1
  
  - Activation 2.1
  
  - Batch 2.1
  
  - Connectors 2.1
  
  - Mail 2.1
  
  - Messaging 3.1
  
  - Authentication 3.0
  
  - Concurrency 3.0
  
  - CDI 4.0
  
  - EL 5.0
  
  - CDI 4.0
  
  - Faces 4.0
  
  - Security 3.0
  
  - Servlet 6.0
  
  - (J)STL 3.0
  
  - (J)SP 3.1
  
  - (JPA)Persistence 3.1
  
  - WebSocket 2.1
  
  - Restful Web Services 3.1
  
  - JSONP 2.1
  
  - JSONB 3.0
  
  - Annotations 2.1
  
  - Interceptors 2.1
  
- Net new

  - But not really new.  Because it's a part of CDI, or a sub spec.

  - CDI Lite 4.0
  
- Not updated

  - EJB 4.0
  
  - Bean Validation 3.0
  
  - Debugging support 2.0
  
  - EJB Lite 4.0
  
  - Managed Beans 2.0
  
  - Transactions 2.0
  
  - DI 2.0

- We don't need to update every spec every time.

- Ivar subtracts the "enterprisy" and you are left with the "Web
  Profile".  We've had a web profile since EE6. Nothing new.
  
  Useful as a smaller platform.
  
   - Auth
   
   - Concurrency
   
   - CDI

   - EL
   
   - Faces
   
   - Security
   
   - Servlet
   
   - STL
   
   - Persistence
   
   - JSP
   
   - WebSocket
   
   - BeanVal
   
   - Debugging
   
   - EJB lite
   
   - Managed beans
   
   - JTA
   
   - JAX-RS
   
   - JSONP
   
   - JSONB
   
   - Annotations
   
   - Interceptors

   - DI
   
   - CDI Lite

- Take away the Webby specs, you get Core Profile. Brand new. EE10.

   - Enables impls to be certified as EE compatible

   - Headless services, or microservices, if you like.
   
   - JAX-RS
   
   - JSONP
   
   - JSONB
   
   - Annotations
   
   - Interceptors
   
   - DI
   
   - CDI Lite 4.0

- Raise the API source level from 8 to 11

   - Why not go further?
   
   - There were not many specs that needed Java features beyond SE 11.
   
   - This allows us to have a broader appeal.
   
   - **TCK runs on 11 or 17.**

- Security 3.0

   - https://jakarta.ee/specifications/security/
   
   - Add support for OPenID Connect!
   
   - Updates to Jakarta Authorization, and Authentication
   
   - This is a foundation for future work (Q.2 Should this be in the
     EE11 talk?)

   - 07:22 Code example for OpenID Connect Authentication. It's an
     annotation. `OpenIdAuthenticationMechanismDefinition` (love the
     long name, very precise)
     
      - Where do you put the annotation?
      
- Persistence 3.1

   - https://jakarta.ee/specifications/persistence/
   
   - Small work interetsed by the community
   
   - Add UUIDs as a basic java data type!
   
      - `GenerationType=UUID`
      
- JAX RS 3.1

   - https://jakarta.ee/specifications/restful-ws/
   
   - Community features, requested a long time.
   
   - Initially wanted a 4.0 release. But went with 3.1 for now.
     Planning a 4.0 release for EE 11 (Q.3 this seems definitely like
     what we want in the talk).
     
   - Multipart form data support.
   
     - RestEASY has this.  JAX-RS catches up now.
      
  - Java SE Bootstrap API
  
     - Lets us run JAX-RS outside of an app server.  (Q.4: this seems
       like a great thing to show with App Service Java SE! What about
       functions?)
       
     - 09:51 Ivar shows a demo https://github.com/ivargrimstad/jakartaee-duke
     
        - booty-duke https://soundcloud.com/edburns00/booty-duke-m4a
        
        - Note that we have explicit dependencies on API and
          IMPL. There is no concept of "provided" scope, where you can
          just depend on the API.
          
        - Uses Maven Assembly plugin
        
        - Needs a main method.
        
           1. Add some configuration: 11:33. Port and root
              path. That's the minimum. There are sensible defaults.
              Other options available.
              
           2. Instantiate the app.
           
           3. Start it. 12:21
           
              - `SeBootstrap.start()`
              
                 - Pass in app and config.
                 
                 - `thenAccept'
                 
              - `Thread.currentThread().join()`
              
   - Run it locally.
   
- Core Profile

   - Reason: target smaller runtimes.
   
   - Limit the amount of specs required to implement it.
   
   - 3 compatible impls
   
      - Open Liberty
      
      - Payara
      
      - WildFly
      
   - Potentially compatible impls

      - Helidon

      - Quarkus

      - Micronaut

      - Helidon and Quarkus have said they will apply for core profile
        compatibility Q.5: What can I say about this in the talk?)

   - CDI Lite
   
      - Enables CDI to be used in GraalVM.
      
      - Make CDI dynamic at build time, not run time.
      
      - Create a new extensions API. Portable extensions is too late.
      
      - Changes how the empty `beans.xml` behaves.
      
         - Prior: empty beans.xml turns on CDI. Everything is injectable.
         
         - Now: beans.xml with only top level XML turns on CDI, but
           Only things that that have the Injectable annotation are
           injectable.
           
- 17:10 Taking a step back.

   - To EE8, how to get to 9.1 and 10.
   
   - If going from 8 to 10, do take the 9 step in between. You don't
     need to go to prod with the 9 version, but it's good to suss out
     problems: new namespace.
     
   - 8 -> 9 big thing: namespace change
   
      - javax.* to jakarta.*
      
      - Why do I care? Because all the things: Spring, Tomcat, Jetty,
        Hibernate, are moving to jakarta namespace.
        
      - It's usually just a import change.
      
      - Tools: Changes the bytecode for you. Can use it at build time
        or runtime. Not recommended to go to th long time.
      
         - Eclipse transformer
         
         - Apache Tomcat Migration Tool
              
      - Tools: IDE help
      
         - IntelliJ Idea has a Java EE to Jakarta EE thing.
         
         - A bit smarter than just a regexp action.
         
      - Manual 20:26
      
         1. Update Jakarta EE version in pom.xml
         
         2. Fix the imports
         
         3. XML schema namespaces
         
         4. Rename properties prefixed with javax.
         
         5. Rename bootstrapping files
         
         6. Verify data and dynamic content
         
         See https://github.com/ivargrimstad/jakartaee-duke/blob/master/complete-duke/README.adoc
       

     - This is a three tier helloworld, to show the migration steps.
     
        - Starts with EE and SE 8 apis.
        
        - Adds EE 8 and 9 to the classpath.  Both of them!
        
        - App is the same as the booty-duke.
        
        - Rest resource.  "hello" path, produces message. Is a
          stateless bean. 23:00
        
           - Finds first message from injected stateful session bean,
             if not there, hard codes the message value.

       - Stateful session bean uses the entity manager from
         Persistence.
         
          - `findAll` method creates a `CriteriaQuery` that basically
            does `SELECT * FROM GREETINGTABLE`. 
            
       - `@Entity DukesGreeting` 23:30
       
       - There is a CDI extension.  `@Dukes`. `DukesExtension
         implements Extension`.
         
          - `processAnnotatedType(@Observes
            @WithAnnotations(Dukes.class)...`
            
          - Prints out a message to the logger for every such
            annotated class.
      
     - 24:10 Now does the namespace change
     
       - Uses the aforementioned IntelliJ Idea tool support.
       
       - **This is why he added the EE 9 and EE8 jars both.**
       
       - Once he ran the tool, he can remove the EE8 jars.
       
     - 24:50 That's steps 1 and 2.
     
     - If you are using XML config, you have to pay attention to that.
     
        - He has a `persistence.xml`.  Uses the JCP namespace.
        
        - 23:58 Replace `jcp.org` with `jakarta.ee`.
        
        - Also change the `schemaLocation`
        
           - version number
           
           - `jcp.org` to `jakarta.ee`
           
        - version number in xml `version` attribute.
     
     - 4. Properties
     
        - he has these in his `persistence.xml` file.  In his case
          this is for drop and create of the table.
          
     - 5. Rename bootstrapping files
     
        - CDI bootstrapping file
          `META-INF/services/javax.enterprise.inject.spi.Extension`
          
        - Rename the file to jakarta.enterprise....
        
     - 6. Verify data and dynamic content. 28:00  Runs it in GlassFish 6
       (EE9) In his case, it's the text in the database. He updates
       the database using some table editor in IntelliJ Idea. Reloads
       the page.
       
        - EE9 supports Java 17.

        - Updates the pom.
        
        - Let's use some Java 17 features as well.
        
           - How about records? 31:27
           
           - Lets us avoid exposing our database architecture in the
             API.
             
           - Creates `DukesGreetingRecord`. 31:49
           
           - In the business logic, use a mapper, map it from the
             greeting to a `new DukesGreetingRecord` populates using
             the getters. 
             
           - Are we done? 33:29.  No.  It' came up empty.  Why?
           
              - JSON Binding is expecting a Java Bean.
              
              - It must have a getter.  Records don't have
                getters. Records are not Java Beans.
                
              - Simplest thing is to change the name of the fields in
                the record: `getDate` `getMessage`.
                
        - Let's upgrade it to EE 10.
        
           - Very simple.
           
           - Update the pom dependency from 9 to 10.
           
           - But is it? No. When running he got a servlet exception.
           
              - Has to do with the `beans.xml` stuff.
              
              - Adds a `beans.xml`. Make sure it's
                version 4. `bean-discovery-mode` should be
                `annotated`.  This is the default, so you can remove
                `bean-discovory-mode`.
                
              - Must also update the schema namespace version.
              
           - 37:44 So great! Can remove the ugly hack we did with the record.  Set
             record fields back to `message` and `date`.
             
   - If you are relying on the "no beans.xml" or "empty beans.xml"
     behavior, then you must take the action.  If you have a
     beans.xml, have it set at annotated, you're fine.
 
- 38:37 the core profile.

   - Takes this application, strips away a bit
   
      - Removes database
      
      - Makes the CDI extension "build compatibile" rather than
        "portable" extension.
        
      - 39:12.  Implements the `spi.Extension` interface. Has
        `processAnnotatedType` method.
        
      - New API in core has `spi.BuildCompatibileExtension`
      
         - Annotations for the different lifecycle events.
         
         - 40:11 Recommendation. If you are running your app in a
           thing that supports full CDI, stick to the portable
           extension. Only if you are running in a CDI lite
           environment should you consider this kind of extension.
           
         - Use the `@Enhancement` annotation.
         
         - Have
           `META-INF/services/jakarta.enterprise.inject.build.compatible.spi.BuildCompatibleExtension`
           identify the extension. This is the package name of the
           interface, so you don't have to remember the long name.
           
   - Runs it in WildFly 27.0.0.Alpha1
   
- Payara Cloud 42:07

   - Upload the application, run it.
   
   - Gives the PaaS value prop: someone else manages the complexity of
     Kubernetes.
     
 - 44:30 We're now thinking of Jakarta EE 11 Q.6
 
    - Discussion: what Java SE level should we base Jakarta EE 11 on?
    
       - Raise to 17?
       
       - Raise to 21?
       
    - How to handle the removal of the `SecurityManager`.
    
       - Users will not notice.
       
       - Impls will have to change.
       
    - New spec: Jakarta Config (finally!)
    
       - https://jakarta.ee/specifications/config/
       
          - More or less based on MicroProfile Config.  Will be
            compatible with it.
            
          - When Jakarta Config comes out, MicroProfile Config will
            probably be retired.
            
          - `@ConfigProperty`
          
   - Possible New spec: Jakarta MVC 

      - https://jakarta.ee/specifications/mvc/
      
      - Server side rendering
      
      - Runs on WildFly, GlassFish, anything with Jersey
      
   - Possible new spec: Jakarta NoSQL
   
      - https://jakarta.ee/specifications/nosql/

   - Possible new spec: Jakarta RPC
   
      - Standardizes gRPC within Jakarta EE

      - https://jakarta.ee/specifications/rpc/
      
   - Possible new spec: Jakarta Data
   
      - Standardizes the repository pattern for data access
      
      - Spring Data, but for Jakarta EE
      
   - Cloud functions?

- Summary 48:00
  

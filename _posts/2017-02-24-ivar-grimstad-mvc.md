---
layout: post
title:  Ivar Grimstad MVC Notes
date:   2017-02-24 15:55 -0500
comments: true
---

Twitter
[@ivar_grimstad](http://twitter.com/ivar_grimstad). [Blog](http://www.agilejava.eu/)
[@mvc_spec](http://twitter.com/mvc_spec).

Ivar had a tough timeslot, the last slot of the last day, but just the
same is doing a great job.

- - -

He is taking over the spec lead role from Manfred and Santiago from
Oracle.

- - -

Agenda

* JSR 371 status

* MVC 1.0

* Q&A.

- - -

Shared the Java EE Roadmap slide from JavaOne, the one including EE8 and
EE9.

- - -

Stated that the decision to drop MVC was influenced by the agressive
schedule for EE8.

Repeated the "Cloud apps often ship headless, making MVC largely
irrelevant".  He said that server side rendering is still something he
uses in his day to day work.

- - -

Showed a screen grab of the transfer ballot.  He restated that even
though he is listed as an individual, he really feels he's representing
the community.

- - -

New slogan, "`@Control`ler to the Community".

- - -

Timeline

August 2014 First Proposal

March 2015 EDR 1

October 2015 EDR 2

January 2017 Transfer Ballot.

- - -

Current activities

Transfer approved by the executive committee

Finalize transfer

Oracle legal is working on the final details

New infrastructure Setup has started

Investigating what license to re-used.  Looking at other specs that are
not lead by Oracle to see how they are licensed.  He has to make sure to
choose a license that will not preclude the possibility of inclusion in
a future Java EE release.

- - -

Future Activities

Formally move the infrastructure from java.net.  They currently do have
the code mirrored from java.net to GitHub, but GitHub is not the scm of
record.

Bring in Christian Kaltepoth as co-spec lead.

Revise the schedule.  Current schedule has 1.0 in first half 2017.
Probably not possible.  It will likely be second half of 2017 or
early 2018.

Adopt-a-JSR: They plan to use it.

- - -

Infrastructure Setup Changes

* code java.net -> [https://github.com/mvc-spec](https://github.com/mvc-spec)

* Issure Tracker [https://github.com/mvc-spec/mvc-spec/issues](https://github.com/mvc-spec/mvc-spec/issues)

* Mailing list jsr371-users@googlegroups.com

* Web site [http://www.mvc-spec.org/](http://www.mvc-spec.org/)

- - -

Adopt-a-JSR

They need help here.  They'd love to have a sample app.

* Write Code

* Blog

* Tweet 

- - -

Upcoming talks

* jDays

* JavaLand

* One other that I missed

- - -

It is an Action-Based MVC.

Currently no standard for this.  It's not a replacement for JSF, as
component based MVC.

* Request hits the controller, updates the model, decides which view to
render.

* In Action-based MVC, the application implements the controller

- - -

Spring MVC, the most used action based MVC framework.  Around
since 2005.

Struts 2, around since 2004, as WebWork2.  Still widely adopted.

He states that MVC will apply the lessons learned from both of these.

- - -

Using existing Java EE technologies.  They will not invent something
unless they need to do so.  This means to run MVC, you need to be
running in a Java EE container.  He mentioned that this causes tension
with the fat jar movement.  He stated that docker run is just as easy to
run as java.jar.

Templating must support Facelets and JSPs.  Model is CDI.  You can use
Bean Validation and JPA as well.

- - -

Decision to build MVC on top of JAX-RS.  It was a close vote, but he
thinks they ended up with the right decision.

- - -

    @Controller
    @Path("hello")
    public class HelloController {

    }

Every method in this class is a controller, because the annotation is at
the class level.

- - -

Views

    @Controller
    @Path("hello")
    public class HelloController {

      @GET
      public String hello() {
        return "hello.jsp"
      }

      @GET
      public String hello() {
        return "hello.jsp"
      }

      @View("hello.jsp")
      @GET
      public void hello() {}


    }

`hello.jsp` is in the "views" folder.

- - -

Models


    @Controller
    @Path("hello")
    public class HelloController {

      @Inject
      private Models model;

      @GET
      public String hello() {
        return "hello.jsp"
      }

      @GET
      public String hello() {
        return "hello.jsp"
      }

      @View("hello.jsp")
      @GET
      public void hello() {}


    }

- - -

Validation


    @Controller
    @Path("hello")
    public class HelloController {

      @Inject
      private Models model;

      @POST
      public Response formPost(@Valid @BeanParam FormDataBean f) {
        return Response.status(OK)...
      }

    }

Can use the ExceptionMapper feature, but it is overly broad.

They added support for binding results.


      @Inject private BindingResult br;

      @POST
      public Response formPost(@Valid @BeanParam FormDataBean f) {
      if (br.isFailed()) {
        // go to error page
      } else {
        return Response.status(OK)...
      }

- - -

Bring your own View Engine

CDI discovery is used to figure out which engine is used.

    public interface ViewEngine {

    boolean supports(String view);

    void processView(ViewEngineContext context) thows ViewEngineException

    }

You can use `@Priority` to control which View Engine is used.

- - -

Events

* Use CDI events.  Spec defines order for events.

* BeforeControllerEvent

* AfterControllerEvent

* ...

CDI `@Observes BeforeControllerEvent`.  Be aware these events are
executed synchronously.

- - -

Security

CSRF prevention: `@CsrfValid` on a JAX-RS http method.  Causes the token
to be added.  Must include `${mvc.csrf.name}` and `${mvc.csrf.token}` as
a hidden field.

- - -

CDI Scopes

You have the standard scopes.  But what about custom scopes?  Did the
POST Redirect Get with the `@RedirectScoped` scope.  Like JSF
`@FlashScope`.  The `@Controller` is request scoped, but the bean is
`@RedirectScoped`.  

- - -

Internationalization

Request Locale.  Can be used for any request Locale.

    @Inject
    private MvcContext mvc;

    @GET
    public String get() {
      Locale locale = mvc.getLocale();
    }
    }

- - -

Tool Support

Even though they are not released the tool support is pretty good.  For
example [jpamodeler.github.io](http://jpamodeler.github.io).

- - -

Demos.

* Simple

* Simple with validation

* Simple with better validation

* Post Redirect Get

* Locale support

* CSRF

* JpaModeler

It was too quick to live blog this, but the demos are at the
[Ivar's github](https://github.com/ivargrimstad/mvc-samples)

- - -

Summary

Action-Based MVC

Uses existing Java EE technologies: JAX-RS, BeanVal, JSF, JSP

- - -

I would like to see some Facelets templating in the samples.

---
layout: post
title:  JAX-RS 2.1 New Feature Introduction
date:   2017-02-22 00:15:00 -0500
comments: true
---
In preparation for my
[DevNexus talk on Thursday]({{ site.baseurl }}{% post_url 2017-02-21-devnexus-2017-picks %}) I
reviewed the commit log for the JAX-RS API for all the commits in
the 2.1 cycle, to be sure I didn't miss anything.  There are basically
two new big ticket features in JAX-RS 2.1.

* Server Sent Events, with support for Reactive Streams as specified in
  the Reactive Streams API

* Reactive Client API

This blog post outlines each feature.  For more details, see the
DevNexus talk.  Thanks to
[Pavel Bucek](https://twitter.com/pavel_bucek),
[Marek Potociar](https://twitter.com/marek_potociar) and
[Santigo Pericas-Geertsen](https://twitter.com/spericas) for co-leading
the JAX-2.1 Spec and in particular for the code samples I cite in this
post.

Please note that this blog post documents in-progress features whose API
will likely change before JAX-RS 2.1 is final.

## Server Sent Events (SSE)

This feature was introduced in "commit 37f8814 Date: Tue Oct 6 20:15:07
2015 +0200" and was substantially enhanced several times since, most
notably with the addition of support for the
[Reactive Streams Standard from Lightbend](http://reactive-streams.org).
Reactive support was very recently introduced, in "commit b2b8f3f Date:
Tue Jan 10 16:36:28 2017 +0100".  SSE is currently the only JAX-RS
feature to use Reactive Streams, but it is specified without being
particularly bound to that feature.  SSE support in JAX-RS makes it easy
to use this feature in a standard way, in both client and server.

Server Sent Events, SSE is a popular web transport technique specified
by the W3C at <https://www.w3.org/TR/eventsource/>.  The most common
usage of SSE is to allow web applications to push one way asynchronous
updates to browsers without having to maintain a million open sockets.
This common usage is well documented at
<https://www.html5rocks.com/en/tutorials/eventsource/basics/>.  As an
aside, because JAX-RS can function as both a client and a server
technology, it is possible to use SSE in a two way fashion if you have
JAX-RS on both ends of your client and server connection.  Either way,
JAX-RS 2.1 has you covered.

Let's take a look at the most up-to-date manifestation of the SSE API in
JAX-RS 2.1.  First, a simple REST resource that implements an SSE server:

<div style="padding: 5px;">
		<div class="java" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">@Path<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;server-sent-events&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">@Singleton</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> ServerSentEventsResource <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">final</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aobject+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Object</span></a> outputLock <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aobject+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Object</span></a><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">final</span> Sse sse<span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">volatile</span> SseEventSink eventSink<span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @Resource</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">private</span> ManagedExecutorService executorService<span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @Inject</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> ServerSentEventsResource<span style="color: #009900;">&#40;</span>Sse sse<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">this</span>.<span style="color: #006633;">sse</span> <span style="color: #339933;">=</span> sse<span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @GET</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @Produces<span style="color: #009900;">&#40;</span>MediaType.<span style="color: #006633;">SERVER_SENT_EVENTS</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> getMessageQueue<span style="color: #009900;">&#40;</span>@<a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Acontext+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Context</span></a> SseEventSink eventSink<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">synchronized</span> <span style="color: #009900;">&#40;</span>outputLock<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">this</span>.<span style="color: #006633;">eventSink</span> <span style="color: #339933;">!=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">throw</span> <span style="color: #000000; font-weight: bold;">new</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aillegalstateexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">IllegalStateException</span></a><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;Server sink already served.&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">this</span>.<span style="color: #006633;">eventSink</span> <span style="color: #339933;">=</span> eventSink<span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @POST</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> addMessage<span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">final</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> message<span style="color: #009900;">&#41;</span> <span style="color: #000000; font-weight: bold;">throws</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aioexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">IOException</span></a> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>eventSink <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">throw</span> <span style="color: #000000; font-weight: bold;">new</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aillegalstateexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">IllegalStateException</span></a><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;No client connected.&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;custom-message&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @DELETE</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> close<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #000000; font-weight: bold;">throws</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aioexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">IOException</span></a> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">synchronized</span> <span style="color: #009900;">&#40;</span>outputLock<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>eventSink <span style="color: #339933;">!=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">close</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @POST</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @Path<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domains/{id}&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; @Produces<span style="color: #009900;">&#40;</span>MediaType.<span style="color: #006633;">SERVER_SENT_EVENTS</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> startDomain<span style="color: #009900;">&#40;</span>@PathParam<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;id&quot;</span><span style="color: #009900;">&#41;</span> <span style="color: #000000; font-weight: bold;">final</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> id,</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; @<a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Acontext+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Context</span></a> SseEventSink eventSink<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; executorService.<span style="color: #006633;">submit</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">-&gt;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">try</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEventBuilder</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">name</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; .<span style="color: #006633;">data</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a>.<span style="color: #000000; font-weight: bold;">class</span>, <span style="color: #0000ff;">&quot;starting domain &quot;</span> <span style="color: #339933;">+</span> id <span style="color: #339933;">+</span> <span style="color: #0000ff;">&quot; ...&quot;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">build</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>200<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span>, <span style="color: #0000ff;">&quot;50%&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>200<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span>, <span style="color: #0000ff;">&quot;60%&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>200<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span>, <span style="color: #0000ff;">&quot;70%&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>200<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span>, <span style="color: #0000ff;">&quot;99%&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>200<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">onNext</span><span style="color: #009900;">&#40;</span>sse.<span style="color: #006633;">newEvent</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;domain-progress&quot;</span>, <span style="color: #0000ff;">&quot;Done.&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSink.<span style="color: #006633;">close</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">catch</span> <span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">final</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Ainterruptedexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">InterruptedException</span></a> <span style="color: #339933;">|</span> <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aioexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">IOException</span></a> e<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; e.<span style="color: #006633;">printStackTrace</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
</ol></div>		</div>

The constructor on line 13 receives the `Sse` class, formerly known as
`SseContext`.  This serves as a factory for instances of
`OutboundSseEvent`, as seen on lines 33 and 57 - 65.

The method that processes GET requests starts on line 17.  This method
receives an `SseEventSink` via injection.  In this simple example, the
GET request is just made to cause the `eventSink` instance variable to
be populated.

Now the magic starts to happen.  `SseEventSink` extends the
`javax.ws.rs.Flow.Subscriber<T>` interface, which is part of a 1:1 copy
of the
[Java SE 9 Flow API](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/Flow.Subscriber.html)
(which is a reactive streams compliant API.  The plan is to drop this
copy in the version of JAX-RS that suppors Java SE 9).  You can see the
`onNext` method of this interface being invoked within the POST
processor on line 33.  Because JAX-RS SSE transparently supports the
Reactive Streams API, you can rest assured that the necessary
requirements to safely maintain a reactive-compliant application are
met.  The meaning of `onNext` is simply, "here's another thing in the
stream of things for you to process".  In this case, the recipient of
the new thing is whatever parties are connected to the other end of the
SSE connection.

A more advanced POST processor is found starting on line 46, for the
path of `domains/{id}`.  A post to this path causes an asynchronous
action to commence using the `ExecutorService`.  This example illustrates the
process of starting an appserver domain on line 54.  Several status
updates are sent on lines 56 - 65 and then the `eventSink` is closed on
line 67.

Rounding out the example, a DELETE processor simply closes the eventSink
on demand, on line 36.

I feel the SSE Client API is more interesting in a sense because it
enables the less common use case of a non-JavaScript SSE client.  Let's
take a look at the SSE Client API in JAX-RS 2.1.

<div style="padding: 5px;">
		<div class="java" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> SseClient <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> WebTarget target<span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> main<span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a><span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span> args<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; target <span style="color: #339933;">=</span> ClientBuilder.<span style="color: #006633;">newClient</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">target</span><span style="color: #009900;">&#40;</span>args<span style="color: #009900;">&#91;</span>0<span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; consumeEventsViaSubscription<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> consumeEventsViaSubscription<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">try</span> <span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">final</span> SseEventSource eventSource <span style="color: #339933;">=</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;SseEventSource.<span style="color: #006633;">target</span><span style="color: #009900;">&#40;</span>target<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;.<span style="color: #006633;">build</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSource.<span style="color: #006633;">subscribe</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Asystem+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">System</span></a>.<span style="color: #006633;">out</span><span style="color: #339933;">::</span>println<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; eventSource.<span style="color: #006633;">open</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">int</span> counter <span style="color: #339933;">=</span> <span style="color: #cc66cc;">0</span><span style="color: #339933;">;</span> counter <span style="color: #339933;">&lt;</span> <span style="color: #cc66cc;">5</span><span style="color: #339933;">;</span> counter<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; target.<span style="color: #006633;">request</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">post</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aentity+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Entity</span></a>.<span style="color: #006633;">text</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;message &quot;</span> <span style="color: #339933;">+</span> counter<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">sleep</span><span style="color: #009900;">&#40;</span>500<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span> <span style="color: #666666; font-style: italic;">// make sure all the events have time to arrive</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">catch</span> <span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Ainterruptedexception+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">InterruptedException</span></a> e<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; e.<span style="color: #006633;">printStackTrace</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
</ol></div>		</div>

Here we come upon an in-progress aspec of the SSE API:
`SseEventSource`.  This class will eventually extend
`Flow.Publisher<InboundSseEvent>`, but in the latest revision of
JAX-RS 2.1, 2.1-m04, it does not.  It does, however, have all necessary the
methods of that interface.  In the above example, the subscribe() method
is seen on line 15.  Here we are simply printing out the messages
received from the server.  Then, on lines 18 - 20 we are issuing a few
POST requests to the server.

## Reactive Client API

This other major feature for JAX-RS 2.1 was introduced in "commit
dea3922 Date: Wed Aug 26 10:26:26 2015 -0400": the Reactive Client API.
This API builds entirely on two foundations: Java 8 CompletableFuture
and the Invocation API from JAX-RS 2.0.  The former is capably explained
by
[Thomas Nurkiewicz](http://www.nurkiewicz.com/2013/05/java-8-definitive-guide-to.html)
whom I had the pleasure of seeing present at
[GeekOut 2015](/blog/2015/06/16/geekout-2015-summary.html).  The latter
is a way to compose client requests and cause them to be processed in
the proper order to enable assembling the responses.  In the case of the
Reactive Client API, these responses are asynchronous and fully reactive
compliant.  The root interface of the API is `RxInvoker`.  This
interface has Java methods for all of the standard HTTP methods, as well
a generic "method" method for arbitrary methods.  The API is usually
invoked as part of a fluid call chain.

The full details of both of these foundational features are too deep to
describe in this blog post, but the following simple example gives a
flavor of their power.

<div style="padding: 5px;">
		<div class="java" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">Client client <span style="color: #339933;">=</span> ClientBuilder.<span style="color: #006633;">newClient</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">URI service <span style="color: #339933;">=</span> URI.<span style="color: #006633;">create</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;http://localhost/service/resource&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">CompletionStage<span style="color: #339933;">&lt;</span>Response<span style="color: #339933;">&gt;</span> cs1 <span style="color: #339933;">=</span> client.<span style="color: #006633;">target</span><span style="color: #009900;">&#40;</span>service<span style="color: #009900;">&#41;</span>.<span style="color: #006633;">request</span>.<span style="color: #006633;">rx</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">post</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aentity+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Entity</span></a>.<span style="color: #006633;">text</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;1&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">CompletionStage<span style="color: #339933;">&lt;</span>Response<span style="color: #339933;">&gt;</span> cs2 <span style="color: #339933;">=</span> client.<span style="color: #006633;">target</span><span style="color: #009900;">&#40;</span>service<span style="color: #009900;">&#41;</span>.<span style="color: #006633;">request</span>.<span style="color: #006633;">rx</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">post</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aentity+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Entity</span></a>.<span style="color: #006633;">text</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;2&quot;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">cs1.<span style="color: #006633;">thenCombine</span><span style="color: #009900;">&#40;</span>cs2, <span style="color: #009900;">&#40;</span>r1, r2<span style="color: #009900;">&#41;</span> <span style="color: #339933;">-&gt;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> s1 <span style="color: #339933;">=</span> r1.<span style="color: #006633;">readEntity</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a>.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> s2 <span style="color: #339933;">=</span> r2.<span style="color: #006633;">readEntity</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a>.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; <span style="color: #000000; font-weight: bold;">return</span> client.<span style="color: #006633;">target</span><span style="color: #009900;">&#40;</span>service<span style="color: #009900;">&#41;</span>.<span style="color: #006633;">request</span>.<span style="color: #006633;">rx</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">post</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Aentity+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Entity</span></a>.<span style="color: #006633;">text</span><span style="color: #009900;">&#40;</span>s1 <span style="color: #339933;">+</span> s2<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">thenAccept</span><span style="color: #009900;">&#40;</span>responseCompletionStage <span style="color: #339933;">-&gt;</span> responseCompletionStage.<span style="color: #006633;">thenAccept</span><span style="color: #009900;">&#40;</span>r3 <span style="color: #339933;">-&gt;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a> s3 <span style="color: #339933;">=</span> r3.<span style="color: #006633;">readEntity</span><span style="color: #009900;">&#40;</span><a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Astring+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">String</span></a>.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Asystem+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">System</span></a>.<span style="color: #006633;">out</span>.<span style="color: #006633;">println</span><span style="color: #009900;">&#40;</span>s3<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></div></li>
</ol></div>		</div>

This example shows a reactive client that issues three separate POST
requests asynchronously, in a non-blocking fashion, and assembling the
result for printing, on line 15.  

## Appendix, work in progress Non-Blocking IO

Another important feature is in the works for JAX-RS 2.1, a Non-Blocking
IO API.  An early draft of this feature was introduced in "commit
e997a32 Date: Thu Sep 3 14:38:00 2015 -0400"

In its final form, this work will likely use the Flow API in a similar
way that the SSE support does.

## Annotated Commit Log Messages

The remainder of the blog post is a review of all the commits to the
JAX-RS 2.1 API since the start of the JSR.

### commit a2b9dfe Date: Fri Feb 10 19:09:46 2017 +0100

Refine javadoc for javax/ws/rs/sse/SseEventSource.java.  Fix the type on
the reference to the accept() method.

### commit 4d79c56 Date:   Fri Feb 10 16:28:15 2017 +0100

Rename Sse.newEvent() to newEventBuilder().  Rename Sse.newDataEvent()
to newEvent().  Update example accordingly.

### commit d62ed77 Date:   Fri Feb 10 15:41:36 2017 +0100

In javax/ws/rs/sse/SseBroadcaster.java, in onException() and onClose(),
modify the type of the argument to be less specific in the inheritance
hierarchy.  It's just a FlowSubscriber<> now, of which SseEventSink is
an implementation.

Refine jaxrs/examples/sse/ItemStoreResource.java example accordingly.


### commit 8a61f14 Date:   Wed Feb 8 09:03:40 2017 +0100

Add newDataEvent() to javax/ws/rs/sse/Sse.java.  This creates an
OutputboundSseEvent.

### commit b036972 Date:   Wed Feb 8 09:43:51 2017 +0100

Refine Javadoc of RxInvokerProvider.

### commit 459ddb6 Date:   Wed Feb 8 18:14:31 2017 +0100

This was the big refactor of the Sse API.

* Drop SseClientSubscriber and remove usages.  Functionality taken over
by new class javax/ws/rs/sse/SseSubscription.java

* Drop SseEventInput

* Rename SseEventOutput to SseEventSink.

* Rename SseContext to Sse.

### commit 2ae0169 Date:   Thu Feb 2 18:43:14 2017 +0100

Introduce javax/ws/rs/sse/SseClientSubscriber.java, an implementation of
Flow.Subscriber().  Drop open() from SseEventSource.  This class was
removed shortly after this commit.

Refine SseClient example to use SseClientSubscriber instead of open().

### commit 4e87a23 Date:   Fri Jan 13 14:29:09 2017 +0100

Rework commit f601630, to remove Class<? extends RxInvokerProvider<T>>
from the signatures, replacing with Class<T>.  Added isProviderFor()
method to javax/ws/rs/client/RxInvokerProvider.java.  Modify
getRxInvoker() to take a SyncInvoker instead of Invocation.Builder.

Updated example javax/ws/rs/core/RxClientTest.java

### commit f601630 Date:   Thu Jan 12 10:31:41 2017 +0100

This was reworked in a subsequent commit.

Refine javax/ws/rs/client/Invocation.java.  Add an rx() method that
takes an RxInvokerProvider.  This enables other types of asynchronous
computation models, such as the RxJava.  There is also a variant that
takes an ExecutorService.

Add javax/ws/rs/client/RxInvokerProvider.java.

Add example javax/ws/rs/core/RxClientTest.java.

### commit b2b8f3f Date:   Tue Jan 10 16:36:28 2017 +0100

Refine jaxrs/examples/sse/ItemStoreResource.java and
jaxrs/examples/sse/ServerSentEventsResource.java to use Java SE 8
lambdas.

Added class javax/ws/rs/Flow.java.  This is the implementation of the
Reactive Streams API.  This is the big class here.

Rework Sse feature to use the Flow API.  In particular, make
SseEventInput an implementation of Flow.Publisher and SseEventOutput an
implementation of Flow.Subscriber.  Also make SseEventSource an
implementation of Flow.Publisher<InboundSseEvent>.

Rework Sse feature to flesh out some hitherto missing Javadoc.

Adde a package-info.java for Sse.


### commit 46d81cf Date:   Thu Oct 15 14:52:43 2015 +0200

Refine ServerSentEventsResource example.

### commit 37f8814 Date:   Tue Oct 6 20:15:07 2015 +0200

First draft of Server Sent Events support.  Since this is one of the
biggest new features in JAX-RS-2.1, I'll discuss it in its final form.

This commit added examples of using SSE in classes
jaxrs/examples/sse/ItemStoreResource.java,
jaxrs/examples/sse/SseClient.java and
examples/sse/ServerSentEventsResource.java.  These classes are still in
the codebase.

* Added forInstance() that takes an Object and returns GenericType to
  javax/ws/rs/core/GenericType.java.  This uses the
  GenericEntity.getType() to return the type, or just returns the Java
  Class.

* Added LAST_EVENT_ID_HEADER constant to
  javax/ws/rs/core/HttpHeaders.java.  This constant is used for the
  "Last-Event-ID" header in the SSE spec <http://www.w3.org/TR/eventsource/#last-event-id>

* Added text/event-stream to javax/ws/rs/core/MediaType.java.

* Added javax/ws/rs/sse/FactoryFinder.java.  Looks like this is used
from SseEventSource to instantiate an SseEventSource.Builder.  This
class has been moved in a later revision of the proposal.

* Added javax/ws/rs/sse/InboundSseEvent.java.  An SseEvent subclass for
  events when JAX-RS client is listening to a server that is sending
  SSE?

* Added javax/ws/rs/sse/OutboundSseEvent.java.  An SseEvent subclass for
events when JAX-RS is acting as the server sending SSE events.

* Added javax/ws/rs/sse/SseBroadcaster.java.  The thing that causes
OutboundSseEvents to be sent to the client.

* Added javax/ws/rs/sse/SseContext.java.  To make the SSE feature
  injectable.  This class has been renamed to Sse in a later version of
  the feature.

* Added class java/javax/ws/rs/sse/SseEvent.java, the event base class.

* Added javax/ws/rs/sse/SseEventInput.java.  Lets you read
  InboundSseEvents as they arrive.  This class has been droped in a
  later version of the feature.

* Added javax/ws/rs/sse/SseEventOutput.java.  Lets you write
OutboundSseEvents. This class has been renamed to SseEventSink a
later version of the feature.

* Added java/javax/ws/rs/sse/SseEventSource.java.  Lets you read
  InboundSseEvents.


### commit f1b7167 Date:   Thu Sep 3 14:41:56 2015 -0400

- Remove onWritePossible() from Response.


### commit e997a32 Date:   Thu Sep 3 14:38:00 2015 -0400

This commit added some experimental work for the use of non-blocking IO
in the JAX-RS spec.  There are helpful examples in
jaxrs/examples/nio/FileResourceClient.java and
jaxrs/examples/nio/FileResource.java.

This commit introduced the following classes:

* javax/ws/rs/client/NioInvoker.java

* javax/ws/rs/core/NioCompletionHandler.java

* javax/ws/rs/core/NioErrorHandler.java

* javax/ws/rs/core/NioInputStream.java

* javax/ws/rs/core/NioOutputStream.java

* javax/ws/rs/core/NioReaderHandler.java

* javax/ws/rs/core/NioWriterHandler.java

And modified the following classes:

* javax/ws/rs/core/Request.java and javax/ws/rs/core/Response.java
  &mdash; Add entity methods that take the Nio*Handler classes above.
  For Response, also add an onWritePossible() method that takes the
  NioWriterHandler.

* javax/ws/rs/client/Invocation.java &mdash; Return the NioInvoker from
  Invocation.nio().

Since this is one of the biggest new features in JAX-RS 2.1, I'll
discuss it in its final form later.  Note that the above classes are all
still present in the API as of today.

### commit dea3922 Date:   Wed Aug 26 10:26:26 2015 -0400

The first iteration of the RxInvoker proposal.  This commit added the
following classes:

* javax/ws/rs/client/CompletionStageRxInvoker.java

* javax/ws/rs/client/RxInvoker.java

And introduced changes to the following classes

* javax/ws/rs/client/Invocation.java

A helpful test is at javax/ws/rs/core/RxClientTest.java.

Since this is one of the biggest new features in JAX-RS 2.1, I'll
discuss it in its final form later.  Note that the above classes are all
still present in the API as of today.

### commit 77464a5 Date:   Wed Feb 18 16:41:44 2015 +0100

Remove the LaTeX sources to a separate repo.
git://java.net/jax-rs-spec~spec

### commit 0dbabe25 Date:   Wed Feb 4 13:28:31 2015 -0500

Spec prose document section 3.2 specifies how Java language fields and
JavaBean properties of Resource classes are injected with values from
the incoming request, all of which are coming over HTTP and are
therefore just plain strings.  That section includes a list that
describes the types of valid values to which those strings can be
converted, let's call them destinations for discussion.  This commit
added List<T>, Set<T>, and SortedSet<T> to set of possible destinations,
where T meets at least one of the following requirements.

* T has a ParamConverter

* T has a public constructor that takes a single String.

* T has a public static valueOf() method that takes a String and returns
an instance of T.

The commit also tightened up what happens when a WebApplicationException
is thrown during the conversion process.


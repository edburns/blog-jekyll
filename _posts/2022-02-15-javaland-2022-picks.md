---
layout: post
title:  "Ed's JavaLand 2022 Session Picks (Updated)"
date:   2022-02-15 19:25-0400
comments: true
---

Cross posted at [dev.to](https://dev.to/edburns00/eds-javaland-2022-session-picks-updated-2ghj).

In my [previous]({{ site.url }}/blog/2021/05/19/build-picks.html) "session picks" post I lamented, "Ever since the end of in-person events, I have not bothered to do my traditional session picks posts".  Well, with [JavaLand 2022](https://javaland.eu) fast approaching, the time has come to resume the practice.

All times in local timezone in JavaLand.  See [the program](https://www.javaland.eu/de/programm/) for more details on each session.

### Monday, 14 March 2022

#### 17:00 - 19:00 Dambali (Matamba) - Pattern matching mit Java

The day before JavaLand is usually reserved for JavaLand kids activities, but with Coronavirus I have not heard about any such plans.  What I do see on the schedule is one talk from German Java conference veteran [Falk Sippach](https://twitter.com/sippsack) and newcomer [Merlin B&ouml;gershausen](https://twitter.com/MBoegie) about pattern matching in Java.  Nope, it's not regular expressions they're talking about.  They are talking about language level features such as pattern matching in switch statements, records, and arrays, as described in this authoritative blog post from [Andrew Binstock](https://blogs.oracle.com/javamagazine/post/java-project-amber-lambda-loom-panama-valhalla).  The talk is in German, but well worth a look.  It's also the only scheduled talk on Monday.

### Tuesday, 15 March 2022

#### 08:30 - 09:10 Schauspielhaus - Unittesting für Jakarta EE - das vernachlässigte Wesen

After many years of working mainly with the arrangement of boxes on architecture diagrams, and the JSON it takes to connect those boxes, I look forward to looking deep inside a single box at the humble Unit Test. Join [Gunnar Hilling](https://github.com/guhilling/) for this Jakarta EE focused look at this neglected discipline.

I also close out my conference picks with a talk about assertions.

#### 09:30 - 10:00 - Silverado - Begrüßung

I've been to every JavaLand ever, and I can attest that Fried's energetic greetings are not to be missed. I am sure it will be especially emotional after we've all been through two years of pandemic hardship. If you are interested in my take on being thankful to be at a conference after two years of pandemic hardship, check out [this previous post]({{ site.url }}/blog/2021/11/22/workshoptage-keynote-summary-document.html).

#### 10:00 - 10:40 - Silverado - Common Misunderstandings in Software Architecture

I am unfamiliar with [Dr. Carola Lilienthal](https://twitter.com/Cairolali), but her book "Sustainable Software Architecture" looks excellent. This talk is likely a distillation of the lessons in the book.

#### 11:00 - 10:40 - Schauspielhaus - Quarkus: a Bliss for Developers

Alex Soto Bueno is delivering a talk on Red Hat's flagship, post-monolith,
cloud-native, enterprise Java developer runtime. I'm confident it will
be true to the legacy of excellent talks from the legendary Burr
Sutter.  Even though this talk competes with Ivar Grimstad's Jakarta
EE 10 talk, I prioritize this one because I see Quarkus as delivery
vehicle for some Jakarta EE specs.  Look at it this way: EE10 is the
future, and some of the EE10 specs will eventually show up in Quarkus.

#### 12:00 - 12:40 - Wintergarden - Low Cost, Cloud Native, Open Standard Java on Azure Kubernetes Service

My biased opinion recommends my own session.  [Emily Jiang](https://twitter.com/emilyfhjiang) and will observe that customers have made it clear: they want Kubernetes, but they want it easy and affordable. It is possible to address these two competing concerns by using open source runtimes on commodity hardware. This lecture shows how to use the EPLv1 licensed Open Liberty runtime on vanilla Azure Kubernetes Service.

If I wasn't giving this talk, I would attend [Johan Janssen's](https://twitter.com/johanjanssen42)
"Why and How to Upgrade to Java 17" in Silverado.  I find an in-person conference is really great for these "one stop shop" sessions.

#### 13:00 - 13:40 - Silverado - The Project that Changed my Mind on Java Modules

Java modules is the feature that should have been in JDK 1.0, but for
many extremely valid reasons did not show up until eleven years
later. Most of the trouble people have had using Java modules in
practice can be traced to necessary decisions projects have had to
make in the absence of such a core feature in the JDK.  The problem of
modularity is fundamental to computer science, and the Java module
system is one answer to that problem for the Java platform.  The
abstract for [Jaap Coomans's](https://twitter.com/JaapCoomans) talk
states, "That’s when the box of Pandora really opened up." Yep, that
is the most common experience of people trying to use Java modules in
practice. I am very curious to see Jaap's answer.

#### 14:00 - 14:40 - Silverado - Debugging Distributed Systems

This topic is solid gold.  In my lexicon "debugging" is the same thing
as "deeply understanding". If you can't debug a system, you can't
claim to deeply understand it.  I expect Java Champion [Bert Jan
Schrijver](https://twitter.com/bjschrijver) will leave you with
actionable lessons on this essential skill.

#### 15:00 - 15:40 - Rotunde - JVM Ergonomics and the Container: Deep Dive

The first thing that popped into my mind when reading [Sascha Selzer's](https://twitter.com/Tommy1199) abstract for "JVM Ergonomics and the Container: Deep Dive" was Martin Thompson's 2017 JavaLand Keynote about [Mechanical Sympathy](https://www.javaland.eu/de/home/news/details/?tx_news_pi1%5Bnews%5D=1327&tx_news_pi1%5Bcontroller%5D=News&tx_news_pi1%5Baction%5D=detail&cHash=bb7d83b09cba83204cb0c902d1700990).  Martin's concept is all about understanding the relationship between the hardware and the software running on top of it.  Given the complete centrality of containerized workloads to our current practice, this talk seems essential.

#### 16:00 - 16:40 - Rotunde - Observing Cloud Native Java Apps Using OpenTelemetry on AWS, GCP and Azure

Sticking with the operations angle, this talk covers OpenTelemetry.  I'd say that OpenTelemetry will do for observability what the Servlet API did for CGI-BIN: make it standard for all Java apps.  Check this talk by [Bernhard Lubomski](https://www.xing.com/profile/Bernhard_Lubomski/cv) out.

#### 17:00 - 17:40 - Wintergarten - OpenId Connect Support in Jakarta EE 10

[Rudy De Bunsscher](https://twitter.com/rdebusscher) is a driving
force behind Payara and also an active Jakarta EE contributor. This
talk demonstrates the continued vitality of Jakarta EE by showing how
the OpenId Connect standard is supported in Jakarta EE 10.  From a
historical perspective, the Security API, introduced by David Blevins
in Jakarta EE 8, is a great foundation for OpenId Connect.  I am curious to see if Spring adopts the Security spec or continues in their
[go your own way](https://www.youtube.com/watch?v=VZAJMBFq85s)
approach when it comes to the "invent vs adopt" decision.

#### 18:00 - 18:40 - Silverado - How to Survive a Live Coding Session

I have observed the world-master of live coding, Dr. Venkat
Subramaniam, and wondered, "how does he do it?" This meta-session
about live coding sessions from [Jacek
Kunicki](https://twitter.com/rucek) offers, "tips and tricks [to] help
you feel more comfortable and prepared for (almost)
everything. Real-life horror stories included!"  Sounds great!

#### 19:00 - 19:40 - Quantum 1+2 - Die neue Gesprächskultur: ganz wie in Panama

I'm glad to see my JSF expert group old colleague [Bernd Müller](https://www.goodreads.com/review/show/378749161) is still doing new and exciting different things in the Java world.  This particular topic of native-Java interaction (but not the GraalVM kind) is close to my heart, having worked with JNI over [20 years ago](https://www-archive.mozilla.org/oji/).

### Wednesday, 16 March 2022

#### 09:00 - 09:40 - Wintergarden - Java at Microsoft - Behind the scenes 

When I first published this post, I had recommended Fabian's talk
titled "State of the Java Metrics Libraries" in Rotunde.  I still want
to see this talk, but I must recommend you attend my colleagues
[Martijn Verburg](https://twitter.com/karianna) and [George
Adams's](https://twitter.com/gdams) talk about the latest entrant in
the OpenJDK ecosystem, Microsoft. You can read more about it in this
blog post announcing [Microsoft joining the JCP](https://devblogs.microsoft.com/java/microsoft-deepens-its-investments-in-java/).

#### 10:00 - 10:40 - Wintergarten - Increase Your Productivity with IntelliJ

My own interviews with [Rockstar Programmers](https://ridingthecrest.com/) support the observation that mastery of tools is the single most important skill in the Rockstar programmer's toolbox. This session from [Bouke Nijhuis](https://twitter.com/BoukeNijhuis), gives you tips for mastery of the most masterful Java IDE.


#### 11:00 - 11:40 - Wintergarten - From Zero to Spring Boot Hero with GitHub Codespaces

Have you heard of GitHub codespaces? Well, [Sandra Ahlgrimm](https://twitter.com/sKriemhild) is a past master of this transformative developer technology.  She joins forces with [Martin Lippert](http://twitter.com/martinlippert) to show how to become super productive with CodeSpaces and Spring Boot.

#### 12:00 - 12:40 - Stock's - Der Application Server ist tot (?) - es lebe Jakarta EE!

My main job at Microsoft is to ensure Java EE application server
workloads have a great home on Azure.  I have seen irrefutable Azure
revenue numbers that prove the Java EE app server is far from dead.
I'm looking forward to an outside perspective from [Dirk Weil](https://twitter.com/dirkweil) on this same question.

#### 13:00 - 13:40 - Wintergarten - Die All-Stars der Software-Bugs – und was wir von ihnen lernen können

I have seen many different anthropomorphisms for software bugs, but "all star team" is a new one. [Christian Seifert](https://twitter.com/perdian_de) brings this entertaining perspective on a fact of life for practicing developers.

#### 14:00 - 14:40 - Schauspielhaus - Feminism for Geeks – A Gentle Introduction

I vehemently challenge anyone who asserts that being "woke" or
"anti-fascist" is a bad thing.  The same thing goes for anyone who
feels that masculinity is somehow under attack. It's not, and if you
believe it is, you believe so in spite of reams of empirical evidence.
I applaud the conference committee for accepting this talk from Wenny
Susanto-Berky and urge you not to miss it.

#### 15:00 - 15:40 - Silverado - Hilfe, ich will meinen Monolithen zurück!

Here we are, roughly seven years since Sam Newman's seminal book
"Building Microservices" and I'm starting to see talks like [Lars Röwekamp's](https://twitter.com/mobilelarson) "Help, I want my
monolith back".  This view is not mere contrarianism. There are
definitely aspects of the monolith that should be preserved, even in a
microservices based architecture.  I'm sure Lars will break it down
for us.

#### 16:00 - 16:40 - Schauspielhaus - Der Java-Werkzeugkasten – Die praktischen Kommandozeilentools des JDK

All of us have been installing JDKs on machines for years and years, but how many know about all the command line tools that get installed in that package? [Michael Hunger](https://twitter.com/mesirii) shows you how to get more from what you already have.

#### 17:00 - 17:40 - Quantum 3+4 - Im Dutzend billiger. Kann's eine Assertion mehr sein? Mit AssertJ zum Testerfolg

There is a whole track on testing at JavaLand, but this is only the second testing talk I've selected. I assure you, this is not because testing is unimportant. Rather, my role as Principal Architect at Microsoft has me working at a higher level of abstraction.  However, I am fascinated by the journey of the humble assertion over time.  The `assert` keyword was added to Java in 2002, but since then the entire practice of CI/CD has evolved.  This talk from [Birgit Kratz](https://twitter.com/bikratz) shows how the concept of assertions is compatible with our contemporary highly-automated development practice.

### Thursday, 17 March 2022

#### 09:00 - 17:00 -  Bambuti (Matamba) - Modern Jakarta EE and Microprofile on Azure: Open Liberty, JBoss EAP, and WebLogic

I'm just going to recommend my own workshop with Sandra Ahlgrimm.
Here's the snappy abstract I wrote.

> You may have heard of the saying, "software is eating the
> world". You may have heard of the concept of "digital
> transformation".  You may be, or know, someone who must implement
> both of these things by moving an existing system to a public cloud.
> Talk about a full stack job! Join Java veteran Ed Burns and Senior
> Developor Advocate Sandra Ahlgrimm for an educational and
> informative deep dive into how to build for Microsoft Azure. This
> workshop will teach Azure concepts by the example of how to easily
> stand up an Azure Kubernetes Service (AKS) and run a Java EE
> application inside of it.

> Participants will explore Oracle WebLogic Server, IBM Open Liberty
> and JBoss EAP as the Java EE runtimes.  We will run the first two of
> these Java EE environments on AKS and the third on Azure App
> Service. As we go along, we will look under the hood at the
> Microsoft, Oracle IBM and Red Hat technologies that enable it all to
> work.

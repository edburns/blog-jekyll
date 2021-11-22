---
layout: post
title:  "Innovation, Gratitude, Compassion and the Cloud - Workshop-Tage Keynote Summary Document"
date:   2021-11-22 08:40-0500
comments: true
---

This document captures the essence of my keynote talk, "Innovation,
Gratitude, Compassion and the Cloud".

<p><img src="{{ site.url }}/blog/assets/20211119-keynote-summary-03.jfif" alt="Layers upon layers of software" /></p>

Every one of us hearing this speech is fortunate enough to work in the
IT industry and can improve the quality of our individual and group
innovations by being thankful for prior innovations and aware of the
tradeoffs that made them succeed over time.  Let's go through each
section of the speech and see how I expanded on that theme.

### Introspection

Here, I set the message: "take some time to think about how we got to
where we are and be thankful for it, and find a way to give back".  I
used a Global -> Local progression.

I'm thankful to be here, especially in the face of COVID-19.  Yes, I
want to say the usual, and appropriate, thanks to Marcus and the team,
and I'm honored to help celebrate 30 years of Workshoptage.  Let's
take a moment to recognize some big things that had to happen to bring
us to this point:

* mRNA vaccines: an idea more than 30 years in the making. If you are
  suspicious about how such a big idea can be developed so quickly,
  rest assured it hasn't been developed quickly.  The mRNA vaccine
  technique started being worked on in earnest [in the late 1990s](https://www.uab.edu/news/youcanuse/item/12059-covid-19-mrna-vaccines-how-could-anything-developed-this-quickly-be-safe).

   Moderna was not the only one working on an mRNA approach to
   vaccines. BioNTech is a German company established to work on
   immunotherapies in 2008 by a Turkish couple, who immigrated to
   Germany.

   On top of this, insights from the fight against flu and HIV were
   crucial creating an effective vaccine for COVID-19.
   
   It is also important to recognize the brave human volunteers who
   accepted the call when governments took the [unprecedented action](https://news.northeastern.edu/2020/10/23/are-covid-19-vaccine-trials-ethical/) of
   relaxing the pace at which human trials [can take place](https://www.bbc.com/news/health-56097088).
   
* Functioning government based on expertise: The indisputable fact
   that viruses do not respect national borders demonstrated the
   importance of nation-states having functional government based on
   expertise, rather than just political expedience. In addition to
   providing fiscal and legal accelerators for vaccine development and
   distribution, governments also took historically unprecedented
   fiscal stimulus actions.  I argued that these were and are helpful.

* Businesses adapting to life with COVID-19: In-person conferences are
   business-travel.  International conferences interact with all
   sectors of the travel industry, all of whom have had major
   adjustments due to COVID-19.  For example, consider the airline I
   always fly to visit Europe:
   
   "Lufthansa’s €9bn ($9.8bn) bail-out allows it to buy 80 new fuel-efficient planes, but it took more money than it needed to preserve its position as a global airline."
      
   Source: [The Economist 2021-08-01](https://www.economist.com/leaders/2020/08/01/airlines-have-a-chance-to-emerge-from-the-crisis-leaner-and-greener)

* Commemoration is also a kind of thankfulness.  Let us remember those
   who died due to COVID-19, even as we are fortunate to be here at
   this event.

   "The standard method of tracking changes in total mortality is “excess deaths”. This number is the gap between how many people died in a given region during a given time period, regardless of cause, and how many deaths would have been expected if a particular circumstance (such as a natural disaster or disease outbreak) had not occurred. Although the official number of deaths caused by covid-19 is now 4.5m, our single best estimate is that the actual toll is 15.2m people. We find that there is a 95% chance that the true value lies between 9.3m and 18.1m additional deaths."

   Source: [The Economist 2021-10-19](https://www.economist.com/graphic-detail/coronavirus-excess-deaths-estimates)

### Reflection

In the middle section, I looked at a few technology domains that
enable today's public cloud and asked about the tradeoffs that were
consciously made by the stewards of that domain to bring the
technology to its present state.  Here are some points that guide the
examination of each domain and its tradeoffs.

* Making the technology gain popularity, but often at the expense of
  correctness and quality

* Consider technologies that solve the harder problems, problems that
  most users don't have or don't know they have. The complexity
  required to solve these harder problems often limits reach.

* Simpler technologies usually win, even if they don't solve the
  harder problems.

* But after they win, is too late to revise the technology to fix the
  harder problems.

   * Security
   * Vulnerability to ransomeware
   * Spam
   * Phishing
   
Here I mention my former colleague at Sun Microsystems Dick Gabriel,
and his brief essay [Worse is Better](https://www.dreamsongs.com/WorseIsBetter.html).
Very interestingly, he mentions the legendary Jamie Zawinski, whom I have always 
esteemed but never managed to meet.

My premise for the middle section is that the technologies that enable
the cloud were built and are continually developed using "Worse is
Better", but we insiders have a responsibility to question that way
and try to at least understand the tradeoffs.

#### Networking

The packet switched networking underpins the entire Internet and all
public clouds was a radical idea when it first emerged.  You must
remember that the telephone industry came from telegrams, then
switchboards for voice, but all of it was connection oriented.

The concept of breaking a message up into little chunks and then putting
them back together when all of them arrive was a radical idea.

Networks, and the companies that ran them, were accustomed to
connection-oriented designs.

Practical capitalism, the kind one which one can build a stable
career, tends to have a problem with radical ideas: They would rather
not be disrupted by them.  Even so, the benefits of packet switched
networking, in terms of cost, resiliency, and flexibility could not be
denied.

Networking is where the idea of interoperability is essential. For
that you need standards.  This gives me an opportunity to riff on the
heroes of standards.

* Working on standards bodies is thankless work.

* Company shareholders want to maximize their investment. Sharing is
  anathema to maximizing profit.  Standards are formalized sharing.

* Individuals that work for corporations, but which support standards,
  often have to fight to prove the business case for standards.

Because networking is so fundamental to the cloud, many of the terms
from networking have seeped into general IT usage, such as "control
plane" "Day minus one, day zero, day one, day two, day N operations",
"North-south" "East-west" on diagrams.

I ended the section with a survey of WAN, LAN, and Consumer services,
showing the winners and losers in each section, and also having an
opportunity to drop some fun old names that no-one hears about any
more.  BITNet, DECnet, Toekn Ring, Minitel and Prodigy for example.

Here are some assorted links of interest for this section.

* https://networkencyclopedia.com/networking-history-1970/

* https://www.wired.com/2012/09/what-do-the-h-bomb-and-the-internet-have-in-common-paul-baran/

* https://spectrum.ieee.org/osi-the-internet-that-wasnt#toggle-gdpr

* https://kb.iu.edu/d/aaso

* https://bit.net/

#### Information representation

In this section I talked about how information is represented.

Just as with the networking world, there was a lot of debate about how
responsibilities should be broken down.

Earlier attempts were informed by our existing thinking of information
being in centralized libraries that are indexed and connected.  This
lead to the creation of distributed index technologies such as Archie
and Gopher. Each node was responsible for indexing the information it
hosted, and sharing the ability to search that index with other nodes.

Then Tim Berners Lee comes along and says 

   YAGNI (you ain't gonna need it), just give me two things: 

   1. The ability to fetch a text file from one computer to another
      without authentication.

      * HTTP GET. The whole method, noun, verb, thing came later.

   2. Some way to look through that text file to find other text files.

Centralized indexing and imparting semantics to larger collections of
text files is outside the scope of his problem.

With centralized indexing, Each server had to know about the others.
When you say YAGNI to this idea, lots of magic happens.

This turned out to be really popular. It lowered the barriers to
entry, and also discarded a lot of the semantic richness of the other
systems.  In the end mass market wins.  Worse is better, right?

However, notice that since the early Web deprioritized the semantic
richness of centralized indexing in favor of ease of authoring and
hosting, there have been numerous efforts to put it back.  Remember
"the semantic web"?  Technorati tags?  One could argue that Google
Page Rank, **utm** tags, and SEO is a way to associate semantic
richness with web pages, but it does so in a completely propietary
way, optimized more for revenue creation than user value.

##### Much older historical aside: Minitel

> https://www.computerhistory.org/timeline/networking-the-web/#169ebbe2ad45559efbc6eb35720e1b49
   
> In 1981, France Telecom offers free Minitel terminals to every phone subscriber, launching the first mass “Web.” Minitel will have tens of millions of users by 1990 and online services such as newspapers, train schedules, tax filing, and erotic classified ads as well as email and chat. The ‘80s Minitel boom heavily foreshadows the dot-com boom. But the business model is different. Customers pay by the minute for access to Minitel services (sites), charged on their phone bills; France Telecom keeps about a third and passes on the rest to the service provider. As in the later Web, Minitel service providers run their own servers. But they also pay France Telecom a fee to connect to its network. Despite major efforts in the US, Canada, and Europe, similar videotex systems will fizzle outside France.

Minitel and other systems needed to have some kind of system to author
content on the system.  Such systems had many of the attributes of
early HTML, but were propietary and did not benefit from economic
network effects for tools and talent.

#### Monetization

In this section I recounted my early direct experiences with the web
and two important developments.

* the invention of the &lt;IMG&gt; tag: NCSA Mosaic. 
* the invention of CGI-BIN.

<p><img src="{{ site.url }}/blog/assets/20211119-keynote-summary-05.png" alt="Layers upon layers of software" /></p>

These two inventions, combined with the ease of authoring for
cross-device reach, even in the early web, attracted the attention of
venture capital. When VC gets involved, the pressure to follow "Worse
is Better" becomes even stronger.  The growth boom of the early Web
spilled over to other technologies as well.  Consider UNIX SMTP based
email.  The protocols that enabled UNIX email to grow and become
widespread did not have any strong concept of aggregated
authentication. The absence of such capability haunts us to this day,
and has created the SPAM and phishing industries.  Perhaps if more
time had been invested making email more authenticated and secure when
email was new, we would not have had as many hacks as we have that
started with phishing.

#### Cloud economics forces on computing hardware.

My time at Sun Microsystems coincided with the introduction of
"Utility Computing", in 2004. This was an idea before its time, and
also at the wrong home. As I explore in the next section, cloud scale
can only be possible with commodity everything, and Sun was a boutique
hardware company.  The early efforts at cloud were also stymied by the
lack of a concept of containers, though Solaris did have Zones. Docker
itself started in a similar way, with LXC, but now using
`libcontainer`.  Happily, the best parts of Solaris are now in
GNU/Linux, such as ZFS and DTrace (shout out to Brian Cantrill).

With these economic forces, we can now have an enormously powerful and
encompassing IT estate managed by a single pane of glass, as shown in
this prophetic video.

<video width="500" height="360" controls>
  <source src="https://www.dropbox.com/s/wal5pt2bgqj6is2/eb-719-blade-runner-zoom-in-finished.mp4?raw=1" type="video/mp4">
  Your browser does not support the video tag.
</video>

#### Commoditization of all aspects of IT infrastructure

All of the forces building in the preceding sections created the
conditions for the public clouds to emerge. The commoditization of
these aspects, are particularly important.

* Networking (software defined and otherwise)
* Compute
* Persistent storage
* Memory
* Platform services

I also observed that the ability for new innovations to build on (with
gratitude) old ones has accelerated.  Consider the anology of
archeology. New cities are built on top of old ones, that's why
archeologists always have to dig to find the old stuff.  We do the
same thing with the cloud.

Look at the layer upon layer of software alone that now exists to run
your Java servlet.

<p><img src="{{ site.url }}/blog/assets/20211119-keynote-summary-01.png" alt="Layers upon layers of software" /></p>

Another example, the design of HTTP/2. It borrows ideas from the
session, transport and network layers of the OSI protocol stack, but
is itself an application layer protocol.

<p><img src="{{ site.url }}/blog/assets/20211119-keynote-summary-02.png" alt="HTTP/2 takes a cue from OSI" /></p>

At this point, I pause to ask the audience to take some time to look
into the history of the technologies you are using now? What are the
ideas on which it is built? What ideas did your current thing reject
as YAGNI?  Was there any part of that rejected idea that you wish had
not been rejected?

### Projection

I closed the keynote with a look forward, taking Philip Armour's five
item list of knowledge representation media as a jumping off point.

* DNA
* Brains
* Hardware design
* Books/Photos/Videos
* Software

But I asked what's next after software?  Perhaps it is just more
software, but this time software that explains itself.  Indeed, this
is at the heart of [machine learning model
explainability](https://towardsdatascience.com/an-overview-of-model-explainability-in-modern-machine-learning-fc0f22c8c29a). For
more on this topic see [Interpretable Machine
Learning](https://christophm.github.io/interpretable-ml-book/) by
Christoph Molnar.

Finally, I ask the audience a final question.

> Is Worse is Better driving the development of today’s world changing technologies, such as AI and nano technology, as it did for the Web, Email, and Social networks?
> If you were in the position to make a Worse is better choice, which would you choose?

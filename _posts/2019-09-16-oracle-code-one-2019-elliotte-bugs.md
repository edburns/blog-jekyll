---
layout: post
title:  "Oracle Code One 2019: Elliotte Rusty Harold Bugs"
date:   2019-09-16 12:30 -0700
comments: true
---

Raw notes from Elliotte Rusty Harold's [@elharo](https://twitter.com/elharo) session

![Elliotte]({{ site.url }}/blog/assets/20190916-elliotte.jpg "Elliotte")


# Plugged Getting Things Done

* The importance of making lists.

* Mind like water

# Every non-trivial problem belongs in the tracker

* Bugs

* New features

* Code health issues

* Docs needed

# No TODOs in code

# If you must use a TODO, have it be a link in the bug tracker

# Make it as easy as possible to file bugs

* Prefer GitHub Issue Tracker

* All info should be optional

# What to do with the issues once they come in?

## Initial Triage

* Schedule a rotating team member to prioritize and assign every bug
  within 24 hours.

* Redirect bugs to appropriate component as needed.

# Assignment

* Assign bugs to individual people, never a team or a group

* When a person leaves, bugs automatically enter the triage queue for
  reassignment.
  
* Everyone reviews and updates bugs assigned to them at least monthly,
  preferably weekly.  (He only has 8 bugs assigned to him at Google).
  
* Distinguish between assigned bugs and active bugs.

# Ongoing Triage

![Elliotte]({{ site.url }}/blog/assets/20190916-elliotte-states.jpg "Elliotte")

"Google doesn't do a good job of" pushed.

# Prioritization

He thinks 5 levels is good.  He also doesn't think it's worthwhile to
separate severity and priority.

* P0: the world is on fire.  IE User data loss. You will spend the money
  it takes to fix this bug.
  
   * Emergency cherrypick and push to fix
   
   * There should be none of these in your bug tracker at any time.
   
* P1: The world is broken.  Drop everything else, but you don't have to
  work nights and weekends.
  
    * Push to production can wait for the next release if it's not too
      far away.
      
    * There should not be a lot of these.  If there are more P1s than
      engineers to fix them, re-prioritize or escalate for more
      resources.
      
* P2: the world is incomplete

   * Major functionality is missing but users can workaround it.
   
   * Schedule and prioritize, don't burn yourself out.
   
   * Higher priority than new features
   
* P4: Trivia

   * Minor technical debt.
   
   * These can be good on-ramps for new open source contributors.
   
   * "Have a stack of these to give out to new developers".
   
# 10X developer == bug hoarder

# Don't freeze your code

* You should be able to commit while a release is in QA or release cut.
  There should not be a time where someone says, "Don't push top master,
  I'm cutting a release."
  
# When there are too many bugs to handle

* Re-prioritize

* What about bug voting?  I don't get the impression developers pay much
  attention to that.
  
  
  
# Do not declare bug bankruptcy! Do not close bugs due to inactivity

* It is a very bad thing.

* It is rude.

* People spent a lot of time submitting the bug.

# Anti patterns that cause bug bankruptcy

* Every uncaught exception files a bug.

* Unmeetable SLA and metrics based on bug count.

# Open source specific issues

* Be friendly to volunteers

* Label bugs as "Ready", "Good First Issue" and "Help wanted"



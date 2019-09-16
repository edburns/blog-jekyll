---
layout: post
title:  "Oracle Code One 2019: Venkat Subramanium"
date:   2019-09-16 09:00 +1100
comments: false
---

Raw notes from Venkat's @venkat_s session.

![Venkat Subramaniam]({{ site.url }}/blog/assets/20190916-venkat.jpg "Venkat")

[Download code samples](http://www.agiledeveloper.com/downloads.html)

# Programming Languages are like Vehicles

* Mastery of the "polyglot" tool.

# Accessing Files

* Groovy and Scala

Showing how to do simple file io in different languages.

* The only JVM language that insists on checked exceptions is Java.  The
  other ones don't.  "The other languages treat programmers as adults".
  
# Interacting with a process

* Groovy

* Garbage collection has a few mistakes.  For example, `finalize`.  GC
  is unpredictable.  "If they want to rename `finalize` it should be
  renamed `badIdea`.  So it has been deprecated.
  
* System.gc() does nothing.  They should rename it to `hope`.


# Why is this more Java than others?

I tuned out to get the picture above, but when I looked up it was still
Java language.  For example, autoclosable.  Showed how to use a factory
method that applies the "execute around method" pattern, but without any
annotation stuff.

In Scala it is the `loan` pattern.

This is useful for policy enforcement.  "One of my most favorite
patterns in programming".

# Traits in Scala

## Highlights

```
trait Friend {
  def listen(name: String) = println("I'm listening...")
}
```

Compiles down to an interface.  What's the big deal?  How does this
differ from default methods in interfaces?  Traits can be "stackable".
You can take multiple traits and stack them for a given object.  When
you call a method on a trait, if it's not implemented it goes up the
chain. Seems like the Action Responder chain in Objective C.

This leads to the ability to do "mixins".

# Joining Strings in Java 8

## Print a list of strings, in uppercase, that are of length 5.

* The stupid comma at the end.

* StringBuilder vs. StringBuffer

* This is an example of simple code that gets too complex.

* The Streams API is easier.  `Collectors`.

* "When I learned about `joining`, I cried that night."

# lazy in Scala and Kotlin, Short Circuiting

* The function is not called when it's not needed.

# Argument Matching in Kotlin

![arg]({{ site.url }}/blog/assets/20190916-venkat-arg-matching.jpg "arg")

I am pretty sure Venkat should take up stand up comedy.

# Auto Casting in Kotlin

# Creating XML Documents in Groovy

* nice way to generate XML from code.

![xml]({{ site.url }}/blog/assets/20190916-venkat-groovy-xml.jpg "xml")

# Tail call optimization in Scala

No time

# Extension methods in Kotlin

* Extension functions do not modify the bytecode.  They become static
  methods somewhere else.
  
* Paves the way to create fluency in your DSLs



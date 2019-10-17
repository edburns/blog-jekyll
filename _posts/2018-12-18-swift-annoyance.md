---
layout: post
title:  "Annoyance about learning Swift"
date:   2018-12-18 14:30 -0500
comments: true
---

I'm pretty sure this is a tell about my mental capacity, but I gotta say
that several features in Swift have the following annoying pattern, at
least in the way they are explained in the official
[Swift Book](https://docs.swift.org/swift-book/).

1. They explain the full syntax of the feature.  This is very nice,
compact, and readily grasped.

2. But wait, you can do the same thing but simpler with this more
concise syntax.  This starts to get obtuse.

3. But wait, you can do it even more simply with this super concise
   syntax.  At this point the syntax itself makes little sense and I
   find it hard to grasp.

Perhaps it would be better to not spring the 2 and 3 level
simplifications on the reader until they have firmly grasped 1?

## Case 1: Closures

The Swift Book helpfully provides the full syntax of closures right at
the front.

    { (parameters) -> return type in
        statements
    }


However, they then go through several, progressively more compact (and
less readily grasped for a beginner) iterations.  At each step along the
way, they point out why the more compact syntax still works, but you
have to keep in your mind other language concepts that are in play to
enable the simplification.  Then there is this cheeky statement:

> There's actually an even shorter way to write the closure expression above.

    reversedNames = names.sorted(by: >)

So, you know all that parameter list, arrow, return type, `in` keyword?
In some special cases, you simply don't need 'em.  Perhaps if I get
heavily into Swift I will come to like the fact that all that seemingly
important stuff can sometimes be omitted, but for now I would rather not
have to learn when it's possible to use the compact syntax and just have
to do the verbose syntax all the time.  Stuff like this just seems like
the language is taking on some things I usually let the IDE do.

## Case 2: Computed Properties

The Swift Book again shows the full syntax of a computed property, but
only by example.  Then it says, "By the way, if your computed property
happens to be read-only, you can simplify the declaration of a read-only
computed property by removing the get keyword and its braces."

## Case 3: Property Observers and the default parameter name

I like the concept of property observers but wonder why they felt the
need to define the `oldValue` and `newValue` default parameter names for
the getter and setter.  Such as:

> You can specify a name for this parameter as part of your willSet
> implementation. If you don’t write the parameter name and parentheses
> within your implementation, the parameter is made available with a
> default parameter name of newValue.

and

> if you implement a didSet observer, it’s passed a constant parameter
> containing the old property value. You can name the parameter or use
> the default parameter name of oldValue.


## Summary

This is certainly not new to Swift.  Indeed I've lamented the same thing
about [Perl](/blog{% post_url
2010-03-03-perils-8220there8217s-more-one-way-do-it8221 %}).  I just
think this "look at how powerful my syntax is with the beautiful
abstractions I've made that allow me to state things so simply"
mentality can be taken a bit too far.


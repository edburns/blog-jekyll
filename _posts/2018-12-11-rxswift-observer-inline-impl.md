---
layout: post
title:  "Inline RxSwift Observer implementation"
date:   2018-12-11 16:00 -0500
comments: true
---

## Executive Summary

My ignorance of the Swift custom of using the `extension` feature to
explode the definition of a single protocol across several source files
lead me to misunderstand how to use the `subscribe` method in RxSwift.

## Details

As I've said [before](/blog{% post_url 2017-06-10-neil-ford-microservices-ujug-march-2017 %}), one of the
things I love about working on my current team at Oracle is the
opportunity to learn new things and put them in practice.  Currently
this means learing Swift and RxSwift.  While doing the usual TDD, I
needed to write several tests that exercise the contract of Rx.  This
required a concise way to pass an argument to the `subscribe` method of
`Observable` that allowed making assertions for the `onNext`,
`onCompleted` and `onError` cases.  As I say in my talk "Programming
Platform Growth: Table Stakes or Deal Makes?" (which I am blessed to be
giving as a keynote at
[JavaLand 2019](https://programm.javaland.eu/2019/#/scheduledEvent/570246)),
a key ingredient of programming platform growth is the welcoming culture
of its developer community.  I have a concrete example here that speaks
well of the Swift community in that regard.

I was looking for examples of passing an obsever to the `subscribe`
method, but all of them passed a single `onNext` handler.
[This post](http://swiftpearls.com/RxSwift-for-dummies-1-Observables.html)
while educational, is indicative of what I could find.

    observable.subscribe(onNext: {(element) in
      print(element)
    })

What I wanted was to pass a complete implementation of the
`ObserverType` with inline methods for its `onNext`, `onCompleted`, and
`onError` functions.  I posed the question in the
[RxSwift slack channel](https://rxswift.slack.com/archives/C051G5Y6T/p1544558825273100)
and the
[Swift #Using forum](https://forums.swift.org/t/closure-or-dynamic-protocol-implementation/18739/2)
and received helpful answers from each.  As a beginner, I failed to
grasp that what looks like an anonymous interface implementation in Java
is actually in invocation of an overloaded method.  You see, in Swift,
it is common to spread out the actual surface area of an API over many
different source files using the Swift `extension` feature.  This was
pointed out to me in the swift.org forum post from Adrian Zubarev.  So,
there is an overloaded `subscribe` method defined in
`ObservableType+Extensions.swift`:

    public func subscribe(onNext: ((E) -> Void)? = nil,
                          onError: ((Swift.Error) -> Void)? = nil,
                          onCompleted: (() -> Void)? = nil,
                          onDisposed: (() -> Void)? = nil)

This is simply a bunch of optional closure parameters, each with their
own signature and default value of `nil`.

Meanwhile, on the real-time help front, danielt1263 and Jeremy offered
some very helpful answers that supplement the answer from swift.org, and
which I actually decided to use.  They suggested using the variant of
`subscribe` that takes a closure that takes an `Event` and returns
`Void`.


<div class="smalltalk" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">let sub <span style="color: #000066; font-weight:bold;">=</span> <span style="color: #00007f;">try</span> getObservable<span style="">&#40;</span><span style="">&#41;</span>.<span style="color: #00007f;">subscribe</span><span style="">&#40;</span><span style="">&#123;</span> event in</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;switch event <span style="">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;case let .<span style="color: #00007f;">next</span><span style="">&#40;</span><span style="color: #00007f;">value</span><span style="">&#41;</span>:</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #00007f;">onNextCalled</span> <span style="color: #000066; font-weight:bold;">=</span> <span style="color: #00007f;"></span><span style="color: #7f007f;">true</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;case let .<span style="color: #00007f;">error</span><span style="">&#40;</span><span style="color: #00007f;">error</span><span style="">&#41;</span>:</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #00007f;">onErrorCalled</span> <span style="color: #000066; font-weight:bold;">=</span> <span style="color: #00007f;"></span><span style="color: #7f007f;">true</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;case .<span style="color: #00007f;">completed</span>:</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #00007f;">onCompletedCalled</span> <span style="color: #000066; font-weight:bold;">=</span> <span style="color: #00007f;"></span>true</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;<span style="">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="">&#125;</span><span style="">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
</ol></div>



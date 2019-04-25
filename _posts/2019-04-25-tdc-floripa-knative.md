---
layout: post
title:  "TDC 2019 Florianópolis: Raw Notes: Serverless with Knative: Mete Atamel"
date:   2019-04-25 10:45 -0300
comments: false
---


![I'm speaking at TDC 2019]({{
   site.url }}/blog/assets/tdc-logo.png "I'm speaking at TDC 2019")

Talk from [Mete Atamel](https://twitter.com/meteatamel).

Also take a look at [his
tutorial](https://github.com/meteatamel/knative-tutorial).

Pronounced "kay native".

# Introduction

K8s open source building blocks for serverless.

Points out that K8s is all about servers.  But Knative hides that.

What is Serverless?

* Servers are someone else's problem

* Functions should be idomatic, meaning already familiar to you

* Event driven

* Portable to other clouds

Asserts Knative ticks all these boxes.

## Gave a bit of Kubernetes Background

How K8s uses a `Service` with a stable IP...

Developers want serverless.  Operators want Kubernetes.  Knative tries
to bridge the gap.

Knative is open source [github](http://github.com/knative).

## Picture: high level Diagram

![Knative Stack]({{
   site.url }}/blog/assets/20190425-tdc-floripa-knative-01-stack.JPG "Knative Stack")

Uses Istio for network, but he feels Istio is too heavyweight for what
they use it for, so they now use Gloo, but even he did not know what
that is.  (This space is moving so fast).

## How to install

Cloud Run or Cloud Run on GKE or Knative Everywhere

A bunch of steps at the command line, mostly with `kubectl`.

The different parts of knative run in different namespaces.

## One part is Knative Serving?

Rapid deployment of serverless containers.  Automatic scaling, traffic
routing, including directing per revisions.  I guess this means it
supports canary, etc.

## Picture: Serving Primitives

![Knative Serving Primitives]({{
   site.url }}/blog/assets/20190425-tdc-floripa-knative-02-serving-primitives.JPG "Knative Serving Primitives")


## Going through steps in tutorial

Before we've even seen any functions, we have a ton of network stuff to
do.  I would like to see the `Dockerfile` for the `helloworld-csharp` C#
Service.

It's been a while since I worked with `Fn`, but Knative seems to have a
lot more friction.  For example, the `Fn` command line tool builds the
docker container around your function for you.  Here you appear to have
to do that yourself.

He used the term "traffic splitting" instead of "blue green" or some
other term.

I don't understand whey he is doing a bunch of `kubectl apply` to set it
up instead of them having Helm chart(s) for this.  I can understand
using `apply` for quick updates when the service is already deployed and
you want to update it.

He didn't mention anything about the overhead, if any, of C# in a
serverless environment.

Showed how `annotations` are used to convey autoscaling.

## Fun: Show integration with Twilio.

Learned that you can put a comma separated list as the argument to
`kubectl get`.  

# Knative Eventing

Connect producers to consumers with stuff in the middle.

Example 1 was not interesting.

Example 2: Integrate Cloud Storage to Vision API

Here comes something like the license plate demo!

All of his examples are in C#.  Is that the only language supported?

# Knative Build

Enables you to build and push the images *within your K8s cluster*.
This is a novel idea.  I talked to [Edson
Yanaga](http://twitter.com/yanaga) last night, he was saying this is the
thing that makes Jenkins obsolete.

RedHat has a [tutorial on this](https://blog.openshift.com/knative-building-your-serverless-service/)






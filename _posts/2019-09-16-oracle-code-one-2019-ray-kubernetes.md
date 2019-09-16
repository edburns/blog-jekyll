---
layout: post
title:  "Oracle Code One 2019: Ray Tsang"
date:   2019-09-16 12:30 +1100
comments: false
---

Raw notes from Ray's @saturnism session.  Ray is always in motion.

![Ray Tsang]({{ site.url }}/blog/assets/20190916-ray.jpg "Ray Tsang")

Surprised that he spent some minutes of his valuable time on the old
"oasis in the desert" story.  Don't get me wrong, it's a great story,
and he tells it very well, but these sessions are only 45 minutes long.
He only spent 3 minutes on it, so I guess it's fine.

# Your App

* Don't start with necessarily start with Kubernetes.  Follow best
  practices.  Start with the Twelve-Factore App!
  
# Test

![Test]({{ site.url }}/blog/assets/20190916-ray-test.jpg "test")

# Containers

* Runtime environment is your responsibility now.

* You'll need a dedicated team for container hygiene.

## Choose a JDK

* Container aware

* OpenJDK 8u192 or above

* Showed the `MaxHeap` problem: if you don't specify, it will take the
  entire available memory, causing Docker to kill you, to illustrate the
  non-container aware JDK problem.
  
   * -XX:+UseParallelGC
   
* Two types of memory usage in Java: Heap, Native Memory Usage

![Memory]({{ site.url }}/blog/assets/20190916-ray-memorycalculator.jpg "Memory")
  
# Docker Tips and Tricks

[http://ridingthecrest.com/blog/2017/02/23/ray-tsang-docker-tips-notes.html](http://ridingthecrest.com/blog/2017/02/23/ray-tsang-docker-tips-notes.html)

# Just Jib It

Jib is a maven plugin, that creates a Docker container .  `jib-maven-plugin`.

![Ray Tsang]({{ site.url }}/blog/assets/20190916-ray-jib.jpg "Ray Tsang")


* This jib thing looks really awesome.  It has full configuration and
  much easier than any docker maven thing I've seen.
  
  [https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)
  
  
# Local Kubernetes

* k3s, k3d, kind, minikube

* k3s is good for linux environments.

* But it looks like the Docker for Mac built-in Kubernetes.

# Keep Base Deployment Simple

The hardest part is to write the yaml file.

```
k create deployment -oyaml --dry-run
k create svc -oyaml --dry-run
```

* Still need to configure `resources` dictionary.  requests, limits, memory, etc.

* Still need to configure liveness (are you alive) and readiness (are
  you ready to accept traffic) probes.
  
* `lifecycle` 

   * SIGTERM.
   
   * `preStop`, httpGet, exec tcpSocket
   
* Put the healthchecks on a separate port, so you can put a network
  security group around it.
  
# Some skaffold thing

* Supports debugging.  Very nice.

# kustomize

* kustomization.yaml







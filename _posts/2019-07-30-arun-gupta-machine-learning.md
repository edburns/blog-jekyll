---
layout: post
title:  "J4K-2019: Arun Gupta: Machine Learning on Kubernetes"
date:   2019-07-30 14:00 -0400
comments: false
---

Raw Notes from Arun Gupta's session.

![Arun Gupta]({{ site.url }}/blog/assets/20190730-arun-gupta.jpg "Arun Gupta")

# Machine Learning 101

![ML 101]({{ site.url }}/blog/assets/20190730-ml-101.jpg "ML 101")

##  Bottom Layer

ML Frameworks and Infrastructures.  For the ML expert practitioners.

* **This is where Kubernetes fits**

##  Middle Layer

ML Services.  Commoditized, managed services.  You don't have time to
train for your own models.

##  Top Layer

AI Services.  Cognitive services: Vision, Speech, Lanugaue, Chatbots,
Forecasting, Recommendation.

## Storage

## Why ML on Kubernetes

* Composability

* Portability

* Scalability

**Mentioned that ML is Stateful**

## Amazon EKS

* Managed Kubernetes control plane, attach data plane

* Managed data plane coming this year

* Native upstream Kubernetes experience.  No forking, patching.

* Integration with additional AWS services.

## Getting Started

* `exsctl`  Installable with brew.

```
brew tap weaveworks/tap
brew install weaveworks/tap/exsctl
eksctl create cluster --node-type=p2.xlarge (GPU powered cluster)
```

Does not install kubectl.  That has to be there already.

## Set up Kubernetes for ML Option 1

* Train: Set up control plane, EKS cluster.

   * Set up as autoscaling group
   
* Inference: Set up another control plane, EKS cluster

This is the dedicated K8s

## Set up Kubernetes for ML Option 2

* Use two separate node groups in one EKS cluster

`nodeSelector role:train`

This is the unified K8s

## Scaling the cluster

* Cluster autoscaler: burstable workloads.  Scale up based on metrics.

* Escalator: Batch or job-based workloads.  More suited to ML.  ML jobs
  run for a long time.  You don't want Kubernetes messing with your
  cluster while a job is running.  Agressively scale up to reduce
  wait-time for pods.
  
They both take over the auto-scaling knob.

## Challenges in setting up containers for ML

* Takes days to configure and test.

* Must optimized for performance and scale.

Re-build and re-optimize.

## AWS Deep Learning Containers

* Optimized and customizable containers for known domains.

* Use these as your base images.

## Touts twice as fast TensorFlow training with AWS-Optimized Tensorflow.

## ML on K8s

* Without KubeFlow

* **Jupyter Notebook** 

## Key Repo 

* https://github.com/aws-samples/machine-learning-using-k8s

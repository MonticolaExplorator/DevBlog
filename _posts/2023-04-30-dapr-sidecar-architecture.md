---
layout: post
title:  "Dapr and the sidecar architecture"
date:   2023-06-10 10:00:00 +0200
permalink: posts/dapr-sidecar-architecture
description: Brief introduction into dapr
categories: [Architecture, Microservices]
image:
  src: /assets/images/2023/june/dapr/dapr-stacked-color.png
  width: 400   # in pixels
  alt: ""
tags: [dapr, microservices, sidecar]
---

Just 2 months ago I had no clue what microservices were. I had heard the term a couple of times and it really made me curious. I was fortunate enough to get my hands on [_Building Microservices_ by Sam Newman](https://www.oreilly.com/library/view/building-microservices-2nd/9781492034018/), and I am enojoying it big time. Some say it is to broad and does not get into specifics. It certainly is not a tutorial, but a set of ideas that paint the big picture of microservices. It lays the foundations so you can evaluate yourself your particular problem, and decide if microservices are the right choice for your problem and what are the key ideas to keep in mind (isolation, independent deployability, decoupling, etc.) while evaluating specific technologies very succintly, almost only mentioning them (Flex, Helm, Docker, etc.).

With this book, I have learned that microservices are a way of architecting software into components that run independently from one another. This means that they must communicate using mechanisms such as http and grpc calls, message queues and databases. The goals that microservices try to achieve is being able to develop, release and deploy components independently. It also has also makes scaling easier, because you can scale each component independetly. But it comes at a cost, for example deployment, testing and diagnosing of a fine grained system is more complex. That is why you need to put all the pros and cons in a balance and decide if the benefits outweigh the drawbacks.

## Dapr

[Dapr](https://dapr.io/) stands for _decentralized application platform_ and it is an open source runtime that you can use it as a building block for decentraliced, multi cloud microservices. It solves the problems that distributed applications face like and it lets you create secure and resilient microservices. It allows you to focus on the logic of your application while Dapr takes care of things like service discovery, observability and secret management, so you can be more productive. 

If you usually develop monoliths, the first thing that might catch your attention is that you won't consume dapr as a set of libraries that you can use to achieve your goals. Instead, dapr runs on it's own process side by side with your applications, using the sidecar pattern. You will invoke the functionality that dapr exposes with http or grpc calls.

## Building blocks

Dapr is based on building blocks, which are a set of APIs that Dapr provides to make your application unaware of the complexity of the challenges that distributed applications face, and also agnostic to the technologies used to face these challenges. Some examples of building blocks that dapr offers include Publish & Subscribe messaging, Service Invocation, Secret Management, Input/Output Bindings and State Management. Let's take [secret management](https://docs.dapr.io/developing-applications/building-blocks/secrets/) for instance, [here is a list of all supported secret stores](https://docs.dapr.io/reference/components-reference/supported-secret-stores/). After choosing the one that you are going to use, you just need to configure it. For example, [here is the configuration for using an Azure KeyVault](https://docs.dapr.io/reference/components-reference/supported-secret-stores/azure-keyvault/). Good news is that you application will be abstracted away from the implementation details of using each different kind of secret management solution, and you will only have to make http requests to your sidecar like http://localhost:3601/v1.0/secrets/localsecretstore/secret

## Best practices

Dapr is open source and part of the [Cloud Native Computing Foundation](https://www.cncf.io/), which means is being contributed by top skilful professionals in the industry and with best practices in mind. Being open source also means that you can contribute to it in case you find a missing component, and the rest of the community can also help maintain and improve your component. One last benefit from this open sourceness is that you can inspect the code and learn a great deal from from the work of others.

## Hosting options

As I mentioned before, dapr runs as a sidecar process next to your app and so you need a way to host dapr. As it could not be otherwise in this era, you can host dapr on [Kubernetes](https://docs.dapr.io/operations/hosting/kubernetes/). Even if you Kubernetes in your production enviroment, you probably need a simpler setup for your dev environment. Luckily, dapr also supports [self-hosted](https://docs.dapr.io/operations/hosting/self-hosted/) modes using [Docker](https://docs.dapr.io/operations/hosting/self-hosted/self-hosted-with-docker/) or [Podman](https://docs.dapr.io/operations/hosting/self-hosted/self-hosted-with-podman/). You can even go serverless and run your _daprized_ solutions on [Azure Container Apps](https://docs.dapr.io/operations/hosting/serverless/azure-container-apps/).

## Is it for you?

Only you know if dapr is the right choice for you. But if you are trying to introduce it on an existing solution, try to do that in small steps so you can evaluate as soon as possible if you are working in the right direction. If you are considering using it on a new solution, it can be a great way to get your ideas to the market fast using tried and true tools.

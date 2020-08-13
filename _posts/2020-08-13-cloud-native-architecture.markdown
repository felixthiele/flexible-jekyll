---
layout: post
title: Cloud Native Architecture
date: 2020-08-13 12:00:00 +00
description: What exactly is cloud native and how to get there? 
img: posts/2020-08-13/title.jpg
tags: [Architecture, Cloud]
---

The cloud native foundation summarizes cloud native as following[^CNF]:

> Cloud native technologies empower organizations to build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds. Containers, service meshes, microservices, immutable infrastructure, and declarative APIs exemplify this approach.<br><br>
> These techniques enable loosely coupled systems that are resilient, manageable, and observable. Combined with robust automation, they allow engineers to make high-impact changes frequently and predictably with minimal toil.

In this blog post, I will describe the different stages on the journey to a cloud native architecture and afterwards dig deeper into the factors that make an application cloud native. 

## The path to cloud native

![Image showing the different stages to move to cloud native as also described below]({{site.baseurl}}/assets/img/posts/2020-08-13/cloud-stages.jpg)
*Stages to cloud native apps [^Cloud-Journey]*

### Cloud Infrastructure Ready app
The easiest but also the least rewarding option to use the cloud is to simply deploy the application onto cloud infrastructure. This might help reduce runtime costs by using cheaper infrastructure resources of a cloud provider. Additionally, it enables limited vertical scaling by making it easier to access more resources. 

### Cloud Optimized app
The second option, which already uses some capabilities of the cloud, is to containerize your application. This allows to spin up instances of the application much easier. Even though the effort involved to bring up the individual application might be drastically reduced, the start up time of the application - due to its monolithic shape - might hinder it to scale up elastically. 

### Cloud Native app
Finally, a cloud native app uses the cloud to its fullest. The system is dissected into self-contained apps that follow for example the twelve-factor app principles as described later in this post, and can be individually deployed and scaled. Starting up a new instance is effortless and immediate, thus spikes in load can easily be mitigated. 

### Comparison
While a simple lift & shift seems like an easy catch, it does not really unlock the capabilities of the cloud, as you only reduce the run-cost and enable an easier increase of computing resources. With a cloud optimized app you start to gain the first real benefits: to containerize the application you have very likely automatized the build process and made the set up of each instance reproducible. You furthermore have connected external systems (like databases) through configurable mechanisms, that make them at least easier to switch out. 

A cloud native app builds on its capability to rapidly scale - which in turn builds on deployment speed. Having a monolithic application with startup times of several minutes / hours is not applicable in this scenario. Therefore cloud native apps aim to slice the monolith into separate business related services, that can be individually deployed and scaled. This approach gives you the advantage of establishing product-centric teams around individual services.

I intentionally did not use the word Microservice here, as I often find that those services are treated as too tiny and not split around a specific capability. Growing a zoo of hundreds or thousands of services ends up pretty quickly in a distributed big ball of mud (also called the Death-Star anti-pattern). Additionally, growing a product-centric team around a microservice does not make much sense, if the service itself is tiny. 

Some applications peak as a cloud optimized app as they do not require the scalability of a cloud native app - and in my opinion that is perfectly reasonable. To design and maintain cloud native applications is more complex and it should be decided on a case by case basis whether to go there.

{% capture cloud_type_info %}
Generally, the term cloud is connected by many to the clouds provided by the likes of Amazon (AWS), Microsoft (Azure) or Google (Google Cloud Platform). Those are all public clouds. But what is the difference between a public and a private cloud? And how does hybrid play in?

### Public Cloud
A public cloud is hosted by a cloud provider and not reserved for any organization. This makes it the cheapest of the three options, as the cloud provider can utilize their resources much more efficiently and distribute them between its customers, leveraging scale effects. The public cloud promises close to unlimited scalability and high availability.  

### Private Cloud
A private cloud may be hosted on-premises or by a cloud provider. In both cases it is only accessible from your own company network - in the latter case by a VPN connection. You may still decide to expose some services to the internet. It is much more tailored to the company using it and offers greater security due to the VPN used. This comes with the drawback of higher costs.

### Hybrid Cloud
The hybrid cloud approach is the combination of the other two. It is often-times chosen in an enterprise environment, when one deals with sensitive data. Processes, that deal with uncritical data might be run in a public cloud whereas highly critical data is only processed by applications hosted on-premises in a private cloud. This requires a very clear cut and a detailed knowledge which data is maintained in the enterprise and how it is processed.
{% endcapture %}

{% include info.html title="Public, Private and Hybrid Cloud" content=cloud_type_info %}

## Principles of a cloud native app

To dig a little deeper now: what exactly does make an app cloud native? One comprehensive and often-used set of principles to design modern cloud native applications are the factors described in the *Twelve-Factor Apps* manifest [^12-factor], which I summarize:

### Codebase
The whole app is located in one version controlled repository. From this repository, the app is deployed to multiple different environments. If there are multiple code bases (e.g. front- and backend) tracked in separate repositories, then we talk about a distributed system and not an app. Those two code bases may individually form  two separate twelve-factor apps.

### Dependencies
All dependencies are explicitly defined. That means based on a *dependency declaration* manifest - a file enlisting all dependencies (e.g. a POM for Maven) - the developers and the build systems can automatically find the required versions of dependencies to build the application.

### Config
The configuration for an app is stored in the environment it is deployed in. As the application may be deployed in very different environments (local developer machine, large productive cloud environment, etc.) the configuration may differ a lot.

### Backing Services
Backing services, like datastores and message queues, are consumed over the network. Switching out the backing service should involve no more work than changing the configuration.

Dealing with backing services in this way allows to choose from the best-in-breed services at all times and for example to adapt fine-tuned services specific to the cloud the application is run in.

### Build, release, run
Moving the codebase to production always involves the following three stages: build, release and run.
1. the codebase is bundled into an executable *build*
2. it is enriched by the environments config to form a *release* 
3. finally it is *run* in the execution environment

### Processes
Twelve-factor apps are executed as one or more processes. Those processes are stateless, meaning any data that needs to be persisted is stored in a backing service. This greatly helps with the robustness of the application, as e.g. when the physical machine fails, a scheduler can easily instantiate that process on different hardware.

### Port binding
All Twelve-Factor apps must expose their services by binding to a configurable port. This allows for example to run multiple services on one machine (e.g. on the developers laptop) and with different configured ports in a cluster.

### Concurrency
Instead of adding more computing resources to one process that deals with concurrency internally (e.g. by the concurrency model of the language), you scale by adding more processes / instances of the application. 

### Disposability
The processes of a twelve-factor app are disposable and thus can be started & stopped gracefully at any time. This allows for elastic scaling, as instances can be deleted fast when not needed and brought up when under heavy load.

### Dev / prod parity
Development, staging and production environments should be kept as similar as possible. This is achieved by catering to the following three areas:

- time: it takes only minutes to hours to release code to production
- personnel: developers are closely integrated into the whole process (DevOps)
- tools: developers use identical tools on their local machine (e.g. database) as used in production

This greatly approves confidence in the continuous deployment process, which is key for agility.

### Logs
Logs are used to reason about the state of an application. A twelve-factor app should communicate its state through event streams and not occupy itself with managing log files. All event streams should be written unbuffered to standard out and the environment should deal with routing those streams. For example one could aggregate the log streams of multiple apps using something like the [ELK-Stack](https://www.elastic.co/what-is/elk-stack).

### Admin Processes
Admin processes, like running database migrations, should be equally stored and shipped with the codebase and run in an identical environment as the application itself.

## Conclusion
In this blog post, we have seen the differences between a cloud infrastructure ready, cloud optimized and cloud native app. We found that not in all cases a cloud native app is the goal and cloud optimized might be a better deal for some occasions. We then looked closely into which factors make a good cloud native app. 

#### Acknowledgements
Thanks to Judith Brecklinghaus, Sylvia Pirgiotis, Jens Brünink and Mathias Voigt for your feedback on the draft of this post.

#### Footnotes
[^CNF]: see <a href="https://github.com/cncf/foundation/blob/master/charter.md" target="_blank">CNF Charter</a>
[^12-factor]: see <a href="https://12factor.net/" target="_blank">12-factor.net</a>
[^Cloud-Journey]: Adapted from <a href="https://docs.microsoft.com/en-us/dotnet/architecture/cloud-native/candidate-apps" target="_blank">Microsoft Docs</a>


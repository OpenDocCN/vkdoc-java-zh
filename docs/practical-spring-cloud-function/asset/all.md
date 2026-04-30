![](images/978-1-4842-8913-6_CoverFigure.jpg)

ISBN 978-1-4842-8912-9e-ISBN 978-1-4842-8913-6 [https://doi.org/10.1007/978-1-4842-8913-6](https://doi.org/10.1007/978-1-4842-8913-6) © Banu Parasuraman 2023 This work is subject to copyright. All rights are solely and exclusively licensed by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors, and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

*I would like to dedicate this book to my wife Vijaya and my wonderful children Pooja and Deepika, who stuck with me through the trials and tribulations during the writing of this book. I also dedicate this to my mom, Kalpana Parasuraman.*

Introduction

I entered the field of Information Technology (IT) 25 years ago, after spending time in sales and marketing. I was an average programmer and was never into hardcore programming. During my early life in IT, I worked as part of a team that built a baseball simulator for the Detroit Tigers. I helped build a video capture driver for that simulator using C++. Even though this was a great project with a lot of visibility, it was never my real passion to be a hard-core programmer.

I soon gravitated toward solution architecture. This seemed to perfectly tie my marketing skills to my technology skills. I began looking at solutions from a marketing lens. This approach formed the basis for writing this book. Because, what good is a technology if we do not know how to apply it in real life?

Functional programming was an emerging technology. Cloud providers such as AWS, Google, and Azure created serverless environments, with innovations such as Firecracker virtualization techniques, that allowed infrastructure to scale down to zero. This allowed customers to derive huge cost savings by not paying for resources that were not in use and subscribing to a pay-per-use model.

Initially, development of these functions that run on serverless environments were built on either NodeJS or Python. These functions were also vendor-specific. Spring.io developed the Spring Cloud Function framework, which allowed the functions to run in a cloud-agnostic environment. The focus was on the “write once, deploy anywhere” concept. This was a game changer in the cloud functions world.

Prior to writing this book, I was a staunch evangelist of Pivotal Cloud Foundry and Kubernetes. I promoted writing code that was portable. When Knative came into being in 2018 as a joint effort between IBM and Google, I was excited. Knative was designed as a serverless infrastructure on top of Kubernetes and made the serverless infrastructure portable. Combining the power and portability of Spring Cloud Function and Knative, you have a true portable system with zero scale-down capabilities.

This was something that I wanted to write and evangelize about. But I felt that writing about the technology and how it worked would not be that exciting. I wanted to write about how people could use this technology in the real world.

In this book, you will see how to program and deploy real-life examples using Spring Cloud Function. It starts with examples of writing code and deploying to AWS Lambda, Google Cloud Function, and Azure Function serverless environments. It then introduces you to the Knative on Kubernetes environment. Writing code and deploying is not enough. Automating the deployment is key in large-scale, distributed environments. You also see how to automate the CI/CD pipeline through examples.

This books also takes you into the world of data pipelines, AI/ML, and IoT. This book finishes up with real-world examples in oil and gas (IoT), manufacturing (IoT), and conversational AI (retail). This book touches on AWS, the Google Cloud Platform (GCP), Azure, IBM Cloud, and VMware Tanzu.

The code for these projects is provided on GitHub at [https://​github.​com/​banup-kubeforce](https://github.com/banup-kubeforce). It is also available at `github.com/apress/practical-spring-cloud-function`. This allows you to get up to speed on the technologies. So, after completing this book, you will have hands-on experience with AI/ML, IoT, data pipelines, CI/CD, and of course Spring Cloud Function.

I hope you enjoy reading and coding this book.

Acknowledgments

It has been a great privilege to write this book and help you understand real-world implementations of Spring Cloud Function. Thank you for reading it.

After my presentation at SpringOne 2020, I received a message on LinkedIn from Steve Anglin at Apress. Steve asked me if I would be willing to write a book about Spring Cloud Function. I was a bit hesitant at first, given that I was occupied with many client engagements, which were taking up most of my work hours. I was worried that I would not do the subject justice, due to my preoccupation with my clients. But after a long contemplation and a heartfelt discussion with my family, I decided to take it on.

I want to thank Steve Anglin, Associate Editorial Director, for reaching out to me and providing me this opportunity to write a book on Spring Cloud Function.

Mark Powers, the Editorial Operations Manager, was instrumental in helping me bring this book to close. With his incessant prodding and nudging, he helped me reached the finish line. Thanks, Mark.

Manuel Jordan, the technical reviewer, was immensely helpful. His comments kept me honest and prevented me from cutting corners. He helped improve the quality of the solutions that I present in this book. Thanks, Manuel.

I also want to thank Nirmal Selvaraj and others at Apress, who worked to bring this book to fruition.

This book would not be possible without the help of my wife Vijaya and daughters Pooja and Deepika, who provided the much-needed emotional support through this journey.

About the Author About the Technical Reviewer

# 1. Why Use Spring Cloud Function

This chapter explores Spring Cloud Function using a sample use case—an HRM (Human Resources Management) system. The focus is on systems that reside in an enterprise. The chapter touches on the FaaS (Functions as a Service) concept and explains how it is gaining momentum in the enterprise. The chapter also digs deeper into its implementations in the cloud. You will learn about some of the portability issues present at the code and container level and read about concepts such as Knative on Kubernetes, which includes container portability. You will also learn about some high-level implementations of Spring Cloud Function on AWS, GCP, Azure, VMware Tanzu, and Red Hat OpenShift.

## 1.1 Functions as a Service (FaaS)

FaaS is a revolutionary technology. It is a great boon for developers and businesses. FaaS allows businesses to adapt to rapidly changing business needs by enabling their development teams to develop products and features at a “high” velocity, thereby improving their Mean Time To Market (MTTM). Developers can develop functions without worrying about setting up, configuring, or maintaining the underlying infrastructure. FaaS models are also designed to use just the right quantity of infrastructure and compute time. They also can be scaled to fit exact demand, by focusing on billing for the number of invocations as compared to billing for uptime. FaaS has two parts, as shown in Figure [1-1](#526597_1_En_1_Chapter.xhtml#Fig1).

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig1_HTML.jpg)

An illustration describes a container containing function code, app server, and operating system. An events cloud flows a consumer to a container, and a container flows a supplier to an events cloud.

Figure 1-1

FaaS component architecture

*   The function code encapsulates the business logic in any language, such as Java, C#, Python, Node, and so on.

*   The underlying container hosts an application server and an operating system.

### 1.1.1 Implementation of an Enterprise Application

Imagine all the infrastructure needed to run a single payroll application on the cloud. This application may consume only 16GB of RAM and eight vCPUs, but you are charged continuously for the entire duration that the application is active. Using a simple AWS pricing formula, this works out to around $1,000 per year. This cost is for the whole time the application is hosted and active, regardless of use. Of course, you can cost-justify it through a TCO (Total Cost of Ownership) calculation, which helps you determine how your application can bring in revenue or value that compensates for the expense. This revenue-generation model is more suitable to applications that generate revenue for the company, such as an ecommerce site. It is more difficult to prove the value that a supporting application, running in the backend of an enterprise, brings to a company.

### 1.1.2 Migration ROI for a Portfolio of Application

The value equation gets more complex if you plan to migrate an extensive portfolio of apps in your enterprise.

Let’s for a moment assume, as a CTO or CIO of a company, you have a portfolio of about one thousand applications that you plan on migrating to the cloud. The key factors to consider, among the many, include:

*   What is the current infrastructure supporting the apps?

*   What is the utilization of these apps?

The utilization of apps is an essential factor in determining the value of the application. Consider this—after analyzing the utilization of apps, you find that this portfolio includes the following distribution:

*   10% with 80% utilization

*   40% with 50% utilization

*   50% with 20% utilization

If you calculate the billing cost using an AWS cost calculator, you see that you will spend $1 million per year. This spend is for applications that are critical and highly utilized, as well as for applications that are minimally utilized. This cost is due to the fact that the cloud providers charge for the entire duration the application is active and consuming the infrastructure. The key here is that the infrastructure is fully allocated for the application’s life. Imagine how much you could save if the infrastructure was allocated only for the duration that the application was active and serving. This would be a great cost and resource saving approach. Cloud providers have thought through this because they also faced the pressure of finite infrastructure and considered the time needed to provision additional infrastructure.

### 1.1.3 The Serverless Functions Concept

To work around the problem of finite infrastructure utilization, AWS created Lambda serverless functions. This was a genius invention. Subscribers to this service pay only for the time the application is invoked. The infrastructure is unallocated when it is not invoked. This way, AWS can save on infrastructure by repurposing the infrastructure for other needy applications while transferring the cost savings to the customer. This is a win-win. It’s worth considering whether you can apply this same approach to all the enterprise applications in your company today. You would be able to save a lot of money. Also, if you were to bring this technology to the datacenter, you would be able to reap the benefits that AWS realized. Isn’t this wonderful?

### 1.1.4 Applying the Serverless Functions Concept to an Enterprise Application

Let’s dig deeper into the concept of functions and how AWS realizes the magic of infrastructure savings. Functions are tiny code pieces with a single input and a single output, and a processing layer (a predicate) acting as the glue. Compare this to enterprise apps, which are designed to do many things. Take a simple payroll system, for example. A payroll system has multiple input interfaces and multiple output interfaces. Here are some of those interfaces:

*   Timecard system to get the hours employees worked in a month

*   Performance evaluation system

*   Peer feedback system

*   Inflation adjustment calculator system

*   The outgoing interface to the IRS

*   The outgoing interface to the medical insurance provider

*   An outgoing interface to the internal web portal where employees can download their paystubs

Running this payroll application is not trivial. I have seen such a payroll system use the following:

*   Fourteen dedicated middleware application servers

*   Two RDBMS database stores

*   Two integration tools such as message queues and FTP

*   Four dedicated bare-metal servers, with each server configured with 128GB RAM, 32 CPUs, 4TB of HDD, 10TB of vSAN, and the like

The key factor in determining whether this application can be hosted on a serverless functions infrastructure like Lambda is the time it takes for the application to boot up (the startup time or cold start) and the time it takes for the application to shut down (the shutdown time). The faster the startup and shutdown times, the larger the cost savings.

It is also important that these times be quick so that they don’t cause disruptions. If you were to research the startup times for large enterprise applications like the payroll application, you would find that it is not pretty. An average startup time is around 15 minutes for all components to come up and another 15 minutes for the application to come down. This would not fly. Imagine if you deployed this application to an AWS Lambda serverless function. Thirty minutes of downtime for each invocation? This will not work. Your users would abandon the application entirely. As you can see, large applications cannot benefit from resource release and resource reassignment because they take a long time to start up and shut down, which would impact the general operation of the application.

Can you make this large payroll application behave in an expected way for serverless functions? The answer is yes. A lot of refactoring is required, but it can be done.

#### Serverless Function in the Cloud

All cloud providers have now incorporated the serverless functions into their infrastructure offerings. AWS has Lambda Functions, Google has Cloud Functions, and Azure has Azure Functions. These providers, in the quest for making their customers captive, made sure to introduce proprietary elements into their environments. The two components that are essential to run the functions are:

*   Serverless function code that serves the functions

*   Serverless infrastructure (containers) that supports the code

#### Why Is It Important for Serverless Functions to be Non-Proprietary?

Enterprises are gravitating toward a multi-cloud, hybrid-cloud approach to their cloud strategy. As you can see in Figure [1-2](#526597_1_En_1_Chapter.xhtml#Fig2), the survey of 3,000 global respondents indicated that 76 percent already work in a multi-cloud environment. This means they are not bound to one single cloud provider. The adoption of a multi-cloud strategy alleviates the risk of vendor lock-in.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig2_HTML.jpg)

A circle diagram depicts the cloud strategy for all organizations. 2% single private, and 9% single public, 89% multi-cloud. Multi-cloud is separated into 80% hybrid cloud, 7% multiple public, and 2% multiple private.

Figure 1-2

Multi-cloud adoption report Source: *https://info.flexera.com/CM-REPORT-State-of-the-Cloud?lead_source=Website%20Visitor&id=Blog*

In a multi-cloud world, it is essential that enterprises subscribe to services that can be easily ported between clouds. This is especially important for commodity services.

FaaS, or serverless functions, have of late become a commodity with all the providers having some services around FaaS. It is therefore imperative that FaaS containers not have proprietary code.

Serverless functions become portable when they do not use proprietary code. Portable serverless functions allow for workload mobility across clouds. If, for instance, AWS Lambda functions are costly and Azure Functions are cheap, enterprises can avail the cost savings and move that Lambda workload to Azure Functions with very little effort.

The subsequent sections discuss in detail these portability issues and explain how you can solve them.

## 1.2 Code Portability Issues

Listing [1-1](#526597_1_En_1_Chapter.xhtml#Par1333) shows the sample AWS Lambda code written in Java. This code was generated using the AWS SAM (Serverless Application Model) template. When observing the code, you can see that the AWS-specific library references and method calls bind the code to AWS. It is not much, but it is potent. In an enterprise, you typically have hundreds if not thousands of pieces of code that you must refactor if you want to move this type of code to another cloud provider. This is a costly affair.

Listing 1-1.Sample Code Using the AWS SAM Framework

![](images/526597_1_En_1_Chapter/526597_1_En_1_Figa_HTML.png)

A framework of amazon web services serverless application model. Package HelloWorld. It describes amazon web service-specific code and method calls that bind the code to amazon web services.

The following section explores the portability of the underlying serverless container, which impacts how multi-cloud migrations are conducted.

### 1.2.1 Serverless Container Portability Issue

What about Lambda’s underlying serverless framework? Is it portable?

If you deep dive into AWS Lambda, the virtualization technology used is Firecracker. Firecracker uses KVM (a kernel-based virtual machine) to create and manage microVMs. You can find more information on Firecracker at [https://​aws.​amazon.​com/​blogs/​aws/​firecracker-lightweight-virtualization-for-serverless-computing/​](https://aws.amazon.com/blogs/aws/firecracker-lightweight-virtualization-for-serverless-computing/).

The minimalist design principle that Firecracker is built on allows for fast startup and shutdown times. Google Cloud Functions, on the other hand, use gVisor and are not compatible with Firecracker. gVisor is an application kernel for containers. More information can be found at [https://​github.​com/​google/​gvisor](https://github.com/google/gvisor).

Azure Functions take a totally different approach of using the PaaS offering app service as their base. So, you can see that the major cloud providers use their own frameworks for the managing functions’ containers. This makes it difficult for functions to move between clouds in a multi-cloud environment. This portability issue becomes more pronounced due to the lack of portability at the container level.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig3_HTML.jpg)

A comparison table. Function codes for Lambda of them are Lambda run time, sandbox, guest O S. For Google, gVisor, Host Kernel, and Google computes engine. For Azure, some of them are language runtime, and web jobs script runtime.

Figure 1-3

Serverless architecture comparison

You can see that the code and containers both differ from the provider and are not easily portable.

In the discussions so far, you have seen the following issues related to FaaS:

*   Portability of code

*   Portability of the serverless container

*   Cold start of the serverless environment

How do you solve these issues?

Enter Spring Cloud Function and Knative. Spring Cloud Function addresses function code portability, and Knative addresses container portability.

Information on Spring Cloud Function is available at [https://​spring.​io/​projects/​spring-cloud-function](https://spring.io/projects/spring-cloud-function), and information about Knative is available at [https://​knative.​dev/​docs/​](https://knative.dev/docs/).

The following sections deep dive into each of these topics.

## 1.3 Spring Cloud Function: Writing Once and Deploying to Any Cloud

As you learned from the earlier discussion, writing functions for AWS Lambda, Google Cloud Functions, or Azure Functions is a proprietary activity. You have to write code specific to a hyperscaler. Hyperscalers refer to large-scale cloud providers like AWS, Google, or Azure, who have a complete mix of hardware and facilities that can scale to 1000s of servers. This is not bad if your strategy is to have a strong single hyperscaler relationship, but over time, when your strategy changes to a hybrid cloud or multi-cloud, you may have to rethink your approach.

> *A hybrid cloud comprises a private cloud and a public cloud and is managed as one entity. Multi-cloud includes more than one public cloud and does not have a private cloud component.*

This is where the Spring Cloud Function comes in. The Spring.io team started the Spring Cloud Function project with the following goals:

*   Promote the implementation of business logic via functions.

*   Decouple the development lifecycle of business logic from any specific runtime target so that the same code can run as a web endpoint, a stream processor, or a task.

*   Support a uniform programming model across serverless providers, as well as the ability to run standalone (locally or in a PaaS).

*   Enable Spring Boot features (auto-configuration, dependency injection, metrics) on serverless providers.

Source: [https://​spring.​io/​projects/​spring-cloud-function](https://spring.io/projects/spring-cloud-function)

The key goals are decoupling from a specific runtime and supporting a uniform programming model across serverless providers.

Here’s how these goals are achieved:

*   Using Spring Boot

*   Wrapper beans for Function<T, R> (Predicate), Consumer<T>, and Supplier<T>

*   Packaging functions for deployments to target platforms such as AWS Lambda, Azure Functions, Google Cloud Functions, and Knative using adapters

*   Another exciting aspect of Spring Cloud Function is that it enables functions to be executed locally. This allows developers to unit test without deploying to the cloud

Figures [1-4](#526597_1_En_1_Chapter.xhtml#Fig4) and [1-5](#526597_1_En_1_Chapter.xhtml#Fig5) show how you can deploy Spring Cloud Function. When Spring Cloud Function is bundled with specific libraries, it can be deployed to AWS Lambda, Google Cloud Functions, or Azure Functions.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig5_HTML.jpg)

A model diagram describes the deployment of the spring cloud function plus K native. It deployed to hyper-scale clouds and on-prem or hybrid.

Figure 1-5

Deploying Spring Cloud Function on Knative serverless configured on Kubernetes environments provided by the cloud providers

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig4_HTML.png)

A model diagram describes the deployment of the spring cloud function. Spring cloud function deployed to Amazon cloud, Google cloud, and Azure using implementation-specific libraries.

Figure 1-4

Deploying Spring Cloud Function directly to FaaS environments provided by the cloud providers

When Spring Cloud Function is containerized on Knative, it can be deployed to any Kubernetes offering, whether on the cloud or on-premises. This is the preferred way to deploy it on hybrid and multi-cloud environments.

## 1.4 Project Knative and Portable Serverless Containers

Having a portable serverless container is also important. This minimizes the complexity and time required to move between cloud providers. Moving between cloud providers to take advantage of discounted pricing goes a long way toward saving costs. One methodology used is called *cloud bursting* (Figure [1-6](#526597_1_En_1_Chapter.xhtml#Fig6)). Cloud bursting compensates for the lack of infrastructure on-premises by adding resources to the cloud. This is usually a feature of a hybrid cloud.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig6_HTML.jpg)

A diagram describes normal traffic flows to hosted applications, and traffic spike flows to burst applications. A corporate data center and public cloud synchronize with hosted and burst applications.

Figure 1-6

Cloud bursting

Figure [1-6](#526597_1_En_1_Chapter.xhtml#Fig6) shows that, to compensate for the lack of resources in a private cloud during a traffic spike, resources are allocated to the public cloud where the traffic is routed. When the traffic spike goes down, the public cloud resources are removed. This allows for targeted use of costs and resources—that is, it uses additional resources only during the traffic spike period. The burst of activity during an eCommerce event like Cyber Monday is a great example of a traffic spike.

This cannot be easily achieved with just a portable code. You need containers that are also portable. This way, containers can be moved across cloud boundaries to accommodate traffic spikes. In Figure [1-6](#526597_1_En_1_Chapter.xhtml#Fig6), you can see that VMs from VMware are used as containers. Since the VMs hosted in the datacenter and hosted in the cloud are similar in construct, cloud bursting is possible.

Applying this to Functions as a Service, you need a new way to make the underlying serverless containers portable.

One such revolutionary approach in the cloud function world is Knative. The next section dives deep into Knative.

### 1.4.1 Containers, Serverless Platforms, and Knative

What was the need for containers /serverless platforms?

Over the course of the evolution of IT, there has been a need for secure isolation of running processes. In the early 90’s, chroot jail-based isolation allowed developers to create and host a virtualized copy of the software system. In 2008 Linux Containers (LXC) was introduced which gave the developers a virtualized environment. In 2011 Cloud Foundry introduced the concept of a container, and with Warden in 2019 container orchestration became a reality. Docker, introduced in 2013, provided containers that can host any operating system. Kubernetes, introduced in 2014, provided the capability to orchestrate containers based on Docker. Finally, Knative, introduced in 2018, extended Kubernetes to enable serverless workloads to run on Kubernetes clusters.

Serverless workloads (Knative) grew out of the need to help developers rapidly create and deploy applications without worrying about the underlying infrastructure. The serverless computing model takes care of provisioning, management, scheduling, and patching and allows cloud providers to develop the “pay only for resources used” model.

With Knative, you can create portable serverless containers that run on any Kubernetes environment. This allows for FaaS to be portable in a multi-cloud or hybrid-cloud environment.

Besides making developers more productive, the serverless environment offers faster deploys (see Figure [1-7](#526597_1_En_1_Chapter.xhtml#Fig7)). Developers can use the “fail fast and fail often” model and spin up or spin down code and infrastructure faster, which helps drive rapid innovation.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig7_HTML.jpg)

A diagram describes the servers deployed in months, virtual machines deployed in minutes, containers deployed in seconds, and serverless deployed in milliseconds.

Figure 1-7

Serverless deploys the quickest

### 1.4.2 What Is Knative?

Knative is an extension of Kubernetes that enables serverless workloads to run on Kubernetes clusters. Working with Kubernetes is a pain. The amount of tooling that is required to help developers move their code from the IDE to Kubernetes defeats the purpose of the agility that Kubernetes professes to bring to the environment. Knative automates the process of build packages and deploying to Kubernetes by provider operators that are native to Kubernetes. Hence, the names “K” and “Native”.

Knative has two main components:

*   *Serving*: Provides components that enable rapid deployment of serverless containers, autoscaling, and point-in-time snapshots

*   *Eventing*: Helps developers use event-driven architecture by providing tools to route events from producers to subscribers/sinks

You can read more about Knative at [https://​Knative.​dev/​docs](https://Knative.dev/docs).

## 1.5 Sample Use Case: Payroll Application

Let’s look at how you can apply serverless functions to a real-life example.

We introduced the payroll application in the beginning of the chapter, we’ll now build on it. Consider a payroll application with the configuration shown in Figure [1-8](#526597_1_En_1_Chapter.xhtml#Fig8).

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig8_HTML.jpg)

A table describes business functions databases and integrations. Some of them are in business functions of reading employee records, and payment information in a database of employee records, and in Integrations of I R S.

Figure 1-8

Payroll application components

Figure [1-9](#526597_1_En_1_Chapter.xhtml#Fig9) shows the configuration.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig9_HTML.png)

A flow diagram describes the employee records and monthly pay data flows to validate employee data to compute salary. Monthly pay rates and tax tables flow to compute salary and some of them are taxes and savings information.

Figure 1-9

Payroll application flow

Translating this flow diagram into an actual implementation that can be deployed in a corporate datacenter results in Figure [1-10](#526597_1_En_1_Chapter.xhtml#Fig10). You see that the functions are developed as REST APIs that are executed in batch mode. The REST APIs expose SAP ECC payroll modules. These REST APIs are run as batch jobs every 15 days. The databases are hosted on Oracle, and integrations are exposed using IBM API Connect (APIC). Note that this is not an on-demand process and can consume a lot of resources when idle. These REST APIs cannot be easily shut down and started up because a typical time for booting up a SAP NetWeaver component can be anywhere from 2 to 15 minutes, depending on the JVM configuration. These application components must be running constantly to keep the payroll application from breaking down.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig10_HTML.jpg)

An architecture diagram describes an Oracle database of employee records and monthly pay data flows to validate employee data to compute salary. Monthly pay rates and tax tables flow to compute salary.

Figure 1-10

Current payroll architecture

Using this use case, the following sections explore how you can leverage Spring Cloud Function to modernize and transform this application into a highly efficient, scalable, and portable system.

## 1.6 Spring Cloud Function Support

The Spring Cloud Function is supported in almost all cloud offerings, as shown in Table [1-1](#526597_1_En_1_Chapter.xhtml#Tab1).

Table 1-1

Spring Cloud Function Support Among Cloud Providers

| AWS | Azure | Google | IBM Cloud | On-Premises |
| --- | --- | --- | --- | --- |
| Lambda | Azure Functions | Cloud Functions | IBM Functions | Tanzu with Knative |
| EKS with Knative | AKS with Knative | GKE with Knative | Tanzu with Knative | OpenShift with Knative |
| Fargate with Knative | ARO with Knative | OpenShift with Knative | OpenShift with Knative | Any Kubernetes offering with Knative |
| ROSA with Knative | Tanzu with Knative | Tanzu with Knative | IBM Kubernetes with Knative |   |
| Tanzu with Knative |   |   |   |   |

Abbreviations: ROSA: Red Hat OpenShift on AWS; ARO: Azure Red Hat OpenShift; EKS: Elastic Kubernetes Services; AKS: Azure Kubernetes Services; GKE: Google Kubernetes Engine

### 1.6.1 Spring Cloud Function on AWS Lambda on AWS

Transforming an application deployed on-premises to leverage an AWS Lambda environment and be portable requires function code that abstracts away the hard dependencies of AWS Lambda from the implementation and the serverless container. This example uses Spring Cloud Function for the function code framework and Lambda for the container. By writing once using Spring Cloud Function, you can use the DevOps pipeline, discussed in subsequent chapters, to deploy to other serverless environments. Figure [1-11](#526597_1_En_1_Chapter.xhtml#Fig11) shows how AWS and its components help realize the payroll application in the cloud.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig11_HTML.jpg)

An architecture diagram describes a relational database of employee records and monthly pay data flows to validate employee data to compute salary. Monthly pay rates and tax tables flow to compute salary.

Figure 1-11

Spring Cloud Function on AWS

Now you need to deploy the payroll system on AWS Lambda. The deployment sequence is important, as you need to deploy SAP ECC and Oracle before integrating and then configure API and messaging for the Spring Cloud Function to integrate with SAP. Spring Cloud Function can be created and tested with dummy information, but it needs to be deployed after integration testing with SAP ECC:

1.  Deploy SAP ECC on the AWS EC2 instances.

2.  Deploy Oracle DB as an RDS instance.

3.  Configure the SAP to Oracle integration.

4.  Deploy Spring Cloud Function to AWS.

5.  Set up the Amazon API Gateway.

6.  Set up Amazon SQS for messaging.

### 1.6.2 Spring Cloud Function on Knative and EKS on AWS

If you want to implement a truly portable environment in AWS, you can leverage the AWS EKS, which is a Kubernetes platform that AWS offers. You can install Knative in EKS and this will give you a truly portable, serverless container that allows for faster deployment and improved cold starts. It uses appropriate technologies, such as Spring Cloud Function on GraalVMs. GraalVMs uses the AOT (Ahead of Time) compilation technique, which significantly improves execution times. Subsequent sections address GraalVMs and Spring Cloud Function. See Figure [1-12](#526597_1_En_1_Chapter.xhtml#Fig12).

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig12_HTML.jpg)

An architecture diagram describes a relational database of employee details flows to validation to compute salary. Pay rates and tax flow to compute salary. It flows through Amazon S Q S to the spring cloud function.

Figure 1-12

Spring Cloud Function on Knative on AWS

Follow this process to deploy the payroll system on Knative hosted on an Azure AKS and ensure that the SAP ECC and Oracle DB are up and integrated. You do this before developing and deploying Spring Cloud Function on Knative:

1.  Deploy SAP ECC on AWS EC2 instances.

2.  Deploy Oracle DB as an RDS instance.

3.  Configure the SAP to Oracle integration.

4.  Set up Knative on an AWS EKS cluster.

5.  Deploy Spring Cloud Function on Knative.

6.  Set up the Amazon API Gateway.

7.  Set up Amazon SQS for messaging.

### 1.6.3 Spring Cloud Function on Cloud Functions on GCP

GCP Cloud Functions provide a cloud alternative to AWS Lambda Functions. The GCP offering is newer than Lambda, but with the Anthos strategy, it is gaining a good amount of the function space. Spring.io works closely with Google to make the Spring.io components work seamlessly with the GCP components.

To deploy the payroll system on Cloud Functions on GCP, follow the process outlined here (see Figure [1-13](#526597_1_En_1_Chapter.xhtml#Fig13)). Ensure that the SAP ECC and Oracle DB are up and integrated before developing and deploying Spring Cloud Function on Cloud Functions:

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig13_HTML.jpg)

An architecture diagram describes an Oracle on G C E V Ms of employee details flows to validation to compute salary. It flows through the cloud pub to the spring cloud function.

Figure 1-13

Spring Cloud Function on GCP

1.  Deploy SAP ECC onto GCE.

2.  Deploy Oracle DB on GCE VMs, as there is no AWS RDS-like service on GCP.

3.  Configure the SAP to Oracle integration.

4.  Set up a GCP Cloud Function project.

5.  Deploy Spring Cloud Function onto GCP Cloud Functions.

6.  Deploy Apigee on GCP to host function APIs.

7.  Set up the Google Cloud pub/sub messaging platform.

### 1.6.4 Spring Cloud Function on Knative and GKE on GCP

Knative, as discussed, is a way to make the functions portable. Knative, incidentally, was created by Google. With GCP, you can set up Knative on GKE, which is the Kubernetes engine provided by Google.

To deploy the payroll system on Knative hosted on a GCP GKE, follow the process outlined here (Figure [1-14](#526597_1_En_1_Chapter.xhtml#Fig14)). Ensure that the SAP ECC and Oracle DB are up and integrated before developing and deploying Spring Cloud Function on Knative:

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig14_HTML.jpg)

An architecture diagram describes an Oracle on G C E V Ms of employee details flows to validation to compute salary. Pay rates and tax flow to compute salary. It flows through the cloud pub to the spring cloud function.

Figure 1-14

Spring Cloud Function on Knative on GCP

1.  Deploy SAP ECC as a Docker image onto the GKE cluster.

2.  Deploy Oracle DB as a Docker image onto the GKE cluster.

3.  Configure the SAP to Oracle integration.

4.  Configure a GKE cluster with Knative.

5.  Deploy Spring Cloud Function onto Knative.

6.  Set up the Apigee API Gateway.

7.  Set up RabbitMQ on GKE for messaging.

8.  Set up Google cloud pub/sub.

### 1.6.5 Spring Cloud Function on Azure Functions on Azure

Spring Cloud Function deployed on Azure Functions is not portable due to the explicit use of an Azure Function Invoker class. While Lambda and Google Cloud Functions require a change to Pom.xml (if you are using Maven), Azure needs an additional class. This makes it less portable. If you have a portfolio of one thousand Spring Cloud Functions in AWS that you need to move to Azure, you have to do a lot of development activity. This is disruptive.

To deploy the payroll system on Azure Functions, follow the process outlined here (see Figure [1-15](#526597_1_En_1_Chapter.xhtml#Fig15)). Ensure that the SAP ECC and Oracle DB are up and integrated before developing and deploying Spring Cloud Function on Azure Functions:

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig15_HTML.jpg)

An architecture diagram describes an Oracle on Azure V Ms of employee details flows to validation to compute salary. It flows through the Azure queue to the Azure plus spring cloud function.

Figure 1-15

Spring Cloud Function on Azure

1.  Deploy SAP ECC on Azure VMs.

2.  Deploy Oracle DB on Azure VMs.

3.  Configure the SAP to Oracle integration.

4.  Configure Azure Functions.

5.  Deploy Spring Cloud Function on Azure Functions.

6.  Set up the Azure API Gateway on Azure.

7.  Set up Azure Queue storage on Azure for messaging.

### 1.6.6 Spring Cloud Function on Knative and AKS on Azure

Knative on Azure AKS is the only option for deploying Spring Cloud Function on Azure that makes it portable. As in any Kubernetes implementation, it requires an implementation of Knative to run the functions. Transforming the payroll application to AKS requires an AKS cluster.

To deploy the payroll system on Knative hosted on an Azure AKS environment, follow the process outlined here (Figure [1-16](#526597_1_En_1_Chapter.xhtml#Fig16)). Ensure that the SAP ECC and Oracle DB are up and integrated before developing and deploying Spring Cloud Function on Knative:

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig16_HTML.jpg)

An architecture diagram describes an Oracle on Azure V Ms of employee details flows to validation to compute salary. It flows through the Azure queue storage to the A K S plus K native spring cloud function.

Figure 1-16

Spring Cloud Function on Knative on Azure

1.  Deploy SAP ECC on Azure VMs.

2.  Deploy Oracle DB on Azure VMs.

3.  Configure the SAP to Oracle integration.

4.  Configure an AKS cluster with Knative.

5.  Deploy Spring Cloud Function onto Knative.

6.  Set up an Azure API Gateway on AKS.

7.  Set up Azure Queue on Azure for messaging.

### 1.6.7 Spring Cloud Function on VMware Tanzu (TKG, PKS)

VMware Tanzu is an evolution of Pivotal Cloud Foundry (PCF). Those who are familiar with PCF should be aware of the “cf push” experience. It was a one-click provisioning approach and was very popular in the developer community. This is the same experience that Knative provides through its Knative build feature. To transform the payroll application to run on VMware Tanzu, you need the Tanzu Kubernetes grid, also known as TKG. TKG is built using the main branch of Kubernetes code. This can be deployed on-premises and in the cloud and can facilitate a multi-cloud or hybrid-cloud strategy. You can start up an instance of TKG on AWS, Azure, or Google by subscribing to the service in the provider’s marketplace.

In a datacenter, you need a collection of servers or a hyper-converged infrastructure like VxRail with PRA (Pivotal Ready Architecture). You also need to upgrade your vSphere to Version 7.

Going back to the payroll application, you need to follow the process outlined here (Figure [1-17](#526597_1_En_1_Chapter.xhtml#Fig17)). Ensure that the SAP ECC and Oracle DB have been up and integrated before developing and deploying Spring Cloud Function on Knative:

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig18_HTML.jpg)

A diagram describes the cluster. It contains Tanzu Kubernetes grid, network, and services. It connected with the K 8s control plane through the v Sphere distributed switch of the management and workload port group.

Figure 1-18

Four-node VxRail P570F cluster for vSphere with Tanzu and HAProxy

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig17_HTML.jpg)

An architecture diagram describes an Oracle on docker plus T K G of employee details flows to validation to compute salary. It flows through the rabbit M Q to the T K G plus K native spring cloud function.

Figure 1-17

Spring Cloud Function on TKG

1.  Deploy SAP ECC as a Docker image onto TKG.

2.  Deploy Oracle DB as a Docker image onto TKG.

3.  Configure the SAP to Oracle integration.

4.  Configure a TKG cluster with Knative.

5.  Deploy Spring Cloud Function onto Knative.

6.  Set up a Spring Cloud Gateway on TKG as an API Gateway.

7.  Set up RabbitMQ on TKG for messaging.

### 1.6.8 Spring Cloud Function on Red Hat OpenShift (OCP)

Red Hat OpenShift can be an on-premises option for deploying Spring Cloud Function. As in any Kubernetes implementation, it requires an implementation of Knative to run the functions. OpenShift has its own serverless implementation, called OpenShift serverless. Transforming the payroll application to OpenShift requires an OpenShift cluster.

To deploy the payroll system to OpenShift hosted on a VMware vSphere environment, follow the outlined process. First ensure that the SAP ECC and Oracle DB are up and integrated before developing and deploying Spring Cloud Function on Knative:

1.  Deploy SAP ECC as a Docker image onto OpenShift cluster.

2.  Deploy an Oracle DB as a Docker image onto the OpenShift cluster.

3.  Configure the SAP to Oracle integration.

4.  Configure an OpenShift cluster with Knative.

5.  Deploy Spring Cloud Function onto Knative.

6.  Set up a Red Hat 3scale API Gateway on OpenShift.

7.  Set up RabbitMQ on TKG for messaging.

![](images/526597_1_En_1_Chapter/526597_1_En_1_Fig19_HTML.jpg)

An architecture diagram describes an Oracle on docker plus open shift of employee details flows to validation to compute salary. It flows through the rabbit M Q to the open shift serverless plus spring cloud function.

Figure 1-19

Spring Cloud Function on Red Hat OpenShift

## 1.7 Summary

This chapter discussed FaaS environments, Spring Cloud Function, and Knative. You saw that FaaS containers/environments provided by AWS, Google, or Microsoft Azure are not portable, as the underlying components that host the FaaS environment do not have the same architecture, which makes it difficult to move or migrate FaaS containers between cloud providers. You also saw that Spring Cloud Function can abstract the dependent AWS and Google libraries and provide a portable alternative. Spring Cloud Function on Knative can improve developer productivity by “writing once and deploying anywhere.” You saw how to apply Spring Cloud Function and Knative to an enterprise payroll application and learned about the various implementation approaches. The next chapter walks through the deployments step-by-step. You will also see how to develop and deploy code to various targets, such as AWS, GCP, Azure, OpenShift, and VMware Tanzu. This will help you understand the power of Spring Cloud Function.

# 2. Getting Started with Spring Cloud Function

This chapter discusses how to develop code using Spring Cloud Function and deploy it to environments in the cloud, on-premises (on a premises datacenter), and on your laptop. It includes a step-by-step approach to coding and deploying the function.

A sample payroll use case of inserting salary details and retrieving salary information is provided. Here are some of the common tasks that need to be performed for each of these environments:

1.  Set up the IDE for the Spring Boot framework.

2.  Add JPA, H2 (this example uses an embedded database to demonstrate the CRUD operations), Spring Cloud Function, and Spring Web Support.

3.  Complete the code for `EmployeeFunction`, `EmployeeConsumer`, and `EmployeeSupplier`.

Note that the code remains the same in all environments; the only change you make is to the `pom.xml` file. There is also an introduction to a `FunctionInvoker` `<Message<String>`, `<String>` class regarding Azure Functions. Azure build looks for this class during the build process. Another difference you will notice with Azure is that you have to use JDK 11 and above to get the Azure templates in the IDE.

The common tasks you will perform for Knative and Kubernetes environments such as EKS, AKS, GKE, OpenShift, and Kubernetes grid are the following:

1.  Create a Docker image of the code.

2.  Set up Knative on all environments.

3.  Deploy the code to Knative.

Deployment of Spring Cloud Function to Knative will demonstrate true portability.

You also see how to use a single IDE such as IntelliJ or VS Code to perform all the functions (i.e., building and deploying to the target environments).

I have used multiple devices to set up the local and on-premises Kubernetes environments. I recommend that VMware VMs be used. You can get the VMware workstation player from VMware for free at [`https://www.vmware.com/products/workstation-player.xhtml`](https://www.vmware.com/products/workstation-player.xhtml). You can also use other alternatives, such as Windows Subsystem for Linux (WSL) or Virtual Box.

All the code is available on GitHub at `https://github.com/banup-kubeforce payroll-h2.git`.

Happy coding!

## 2.1 Setting Up the Spring Boot and Spring Cloud Function Locally

This is a preliminary step that you undertake to determine if the Spring Cloud Function works. This will set the stage for deployment.

Prerequisites:

*   An IDE such as IntelliJ, Eclipse, Spring IDE, or Red Hat Code Ready workspace

*   Maven or Gradle

*   Postman for testing

*   Code from GitHub at [`https://github.com/banup-kubeforce/payroll.git`](https://github.com/banup-kubeforce/payroll.git)

Here are the steps:

*   Step 1: Create the Spring Boot scaffolding.

*   Step 2: Create the employee model.

*   Step 3: Write the consumer.

*   Step 4: Write the supplier.

*   Step 5: Write the function.

*   Step 6: Deploy and run the code locally.

*   Step 7: Test the function.

I am using IntelliJ, as it makes me more productive with all the capabilities such as plugins for the cloud, Docker, Maven, and so on.

### 2.1.1 Step 1: Create the Spring Boot Scaffolding

In IntelliJ, create a Spring Boot project using Spring Initializer. Make sure you provide the name and choose JDK 11\. Then provide a name and a group name. Click Next to reach the New Project screen, as shown in Figure [2-1](#526597_1_En_2_Chapter.xhtml#Fig1).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig1_HTML.jpg)

An application screenshot. The left side contains project types and the right side contains new project configurations. Some of them are severed U R L, name, location, language, type, group, and artifact.

Figure 2-1

Spring Initializer set up in IntelliJ

On the next screen, it is important to select Spring Web and Function. If you just choose Function, you will not be able to test the function locally, as the functions will not persist. See Figure [2-2](#526597_1_En_2_Chapter.xhtml#Fig2).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig3_HTML.jpg)

A screenshot of the spring boot deployment logs. It allows for the function to be tested by tools.

Figure 2-3

Spring Boot deploy and run

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig2_HTML.jpg)

An application screenshot of a new project. It contains a set of dependencies on the left side. From dependencies, a function is selected, Function description and add dependencies are on the right side.

Figure 2-2

Pick the dependencies for the project

As you can see from the Added Dependencies section in Figure [2-2](#526597_1_En_2_Chapter.xhtml#Fig2), I also included an H2 Database. You can use the database of your choice.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig4_HTML.jpg)

A screenshot of testing in postman. The page contains authorization, headers, body, pre-request script, tests, and settings. The body contains the name, employee identifier, email, and salary.

Figure 2-4

Testing in Postman

Upon clicking the Finish button, the IDE will create the code bits and take you to the project screen. Alternatively, you can bring the code down from GitHub by cloning the project.

Listing [2-1](#526597_1_En_2_Chapter.xhtml#PC1) shows the `pom.xml` file with Maven dependencies.

```
org.springframework.boot
spring-boot-starter-data-jpa

org.springframework.boot
spring-boot-starter-data-rest

org.springframework.boot
spring-boot-starter-web

org.springframework.cloud
spring-cloud-function-web

com.h2database
h2
runtime

Listing 2-1
pom.xml File with Maven Dependencies
```

Listing [2-2](#526597_1_En_2_Chapter.xhtml#PC2) shows the `application.properties` file. I included `spring.h2.console.enabled=true` to be able to verify if the database has been populated.

```
spring.cloud.function.definition=employeeConsumer
spring.datasource.url=jdbc:h2:mem:employee
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=create
spring.h2.console.enabled=true
Listing 2-2
application.properties
```

`PayrollApplication.java`, as shown Listing [2-3](#526597_1_En_2_Chapter.xhtml#PC3), is the main entry point for the application. It is important to define three beans that represent the consumer, supplier, and function, so that these functions can be accessed after deployment.

If you do not do this, you will not be able to invoke the function.

```
package com.kubeforce.payroll;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.function.context.FunctionalSpringApplication;
import org.springframework.context.annotation.Bean;
@SpringBootApplication
public class PayrollAwsApplication {
public static void main(String[] args) {SpringApplication.run(PayrollApplication.class,args);
//SpringApplication.run can be used to run the function application locally.
//Change SpringApplication.run to FunctionalSpringApplication.run when deploying to a serverless platforms
// such as lambda, knative, etc.
}
@Bean
public EmployeeFunction employeeFunction() {
return new EmployeeFunction();
}
@Bean
public EmployeeConsumer employeeConsumer() {
return new EmployeeConsumer();
}
@Bean
public EmployeeSupplier employeeSupplier() {
return new EmployeeSupplier();
}
}
Listing 2-3
PayrollApplication.java
```

Once the scaffolding and code bits are completed, you are ready for the next step, which is to create the model for your employee.

### 2.1.2 Step 2: Create the Employee Model

The model creates the getters and setters for the ID, name, employee ID, email, and salary.

I created this manually, but you can generate this using the Spring JPA data model generator for data that resides in an RDBM such as Oracle, MYSQL, or SQL Server, or a document database such as MongoDB. The database support for the generation of code using Spring JPA depends on the adapter and can be found at [`https://docs.spring.io/spring-integration/reference/html/jpa.xhtml#jpa`](https://docs.spring.io/spring-integration/reference/html/jpa.xhtml%2523jpa).

The model created in Listing [2-4](#526597_1_En_2_Chapter.xhtml#PC4) allows for CRUD operations into the H2 Database.

```
package com.kubeforce.payroll;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;
@Entity
@Table(name= "employee")
public class Employee {
@Id
@GeneratedValue(generator = "UUID")
private Long id;
private String name;
private int employeeid;
private String email;
private String salary;
public Employee(String name, int employeeIdentifier, String email, String salary)
{
this.name = name;
this.employeeid = employeeIdentifier;
this.email = email;
this.salary = salary;
}
public Employee() {
}
public String getName ()
{
return name;
}
public void setName (String name)
{
this.name = name;
}
public int getEmployeeIdentifier ()
{
return employeeid;
}
public void setCustomerIdentifier (int employeeIdentifier)
{
this.employeeid = employeeIdentifier;
}
public String getEmail ()
{
return email;
}
public void setEmail (String email)
{
this.email = email;
}
public String getSalary ()
{
return salary;
}
public void setSalary (String salary)
{
this.salary = salary;
}
public Long getId ()
{
return id;
}
public void setId (Long id)
{
this.id = id;
}
}
Listing 2-4
Employee Entity Model
```

Once the model is created/generated, you can proceed with coding the consumer that will write to the database and the supplier that will read from the database.

### 2.1.3 Step 3: Write the Consumer

In this step, you write the consumer that will write to the database. Here, you expose the function attributes such as name, employeeIdentifier, email, and salary, which enable you to pass the data to the function.

```
package com.kubeforce.payroll;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
public class EmployeeConsumer implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(EmployeeConsumer.class);
@Autowired
private EmployeeRepository employeeRepository;
@Override
public void accept (Map map)
{
LOGGER.info("Creating the employee", map);
Employee employee = new Employee (map.get("name"), Integer.parseInt(map.get(
"employeeIdentifier")), map.get("email"), map.get("salary"));
employeeRepository.save(employee);
}
}
Listing 2-5
Employee Entity Model
```

### 2.1.4 Step 4: Write the Supplier

The supplier function in this example allows you to get all the data in the database. You can change it to suit your needs.

Note that you will be using the Spring Data Repositories to interact with the database. This is a common way for both Spring Data REST and Spring Cloud Function.

```
package com.kubeforce.payrol;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import java.util.List;
import java.util.function.Supplier;
@Component
public class EmployeeSupplier implements Supplier
{
public static final Logger LOGGER = LoggerFactory.getLogger(EmployeeSupplier.class);
@Autowired
private EmployeeRepository employeeRepository;
@Override
public Employee get ()
{
List employees = employeeRepository.findAll();
LOGGER.info("Getting the employee of our choice - ", employees);
return employees.get(0);
}
}
Listing 2-6
EmployeeSupplier.java
```

### 2.1.5 Step 5: Write the Function

This step is optional, and you can see from the code that you can create a function definition outside of the consumer and supplier and allow for getting the data associated with an specific ID. See Listing [2-7](#526597_1_En_2_Chapter.xhtml#PC7).

```
package com.kubeforce.payroll;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Optional;
import java.util.function.Function;
public class EmployeeFunction implements Function  {
@Autowired
private EmployeeRepository employeeRepository;
@Override
public Employee apply (Long s)
{
Optional employeeOptional = employeeRepository.findById(s);
if (employeeOptional.isPresent()) {
return employeeOptional.get();
}
return null;
}
}
Listing 2-7
EmployeeFunction.java
```

Once you have developed your code, you can run the function locally. The key step to run this locally is to modify the main class to include:

```
SpringApplication.run(PayrollApplication.class, args)
```

For deployment to other environments such as Lambda, use:

```
FunctionalSpringApplication.run(PayrollApplication.class, args)
```

The use of `SpringApplication` allows you to keep the code up and running so that you can test the function with tools such as Postman.

### 2.1.6 Step 6: Deploy and Run the Code Locally

In this step, you run the code just like you would for any other Spring Boot code.

Note that the code runs and is available in port 8080.

This allows for the function to be tested by tools such as Postman and curl.

### 2.1.7 Step 7: Test the Function

You can use curl or Postman to test your function. Here I use Postman. You can download Postman at [`https://www.postman.com/downloads/`](https://www.postman.com/downloads/).

In Postman, choose `POST`, the `http://localhost:8080/employeeConsumer` URL, and the JSON format input.

```
{
"name": "xxx",
"employeeIdentifier":"2",
"email": xxx@yahoo.com,
"salary":"1000"
}
```

You can test all the three interfaces here, but when you go to Lambda or other environments, you will be restricted to just one interface.

You can also verify if the data has been added by going to the H2 console, as shown in Figure [2-5](#526597_1_En_2_Chapter.xhtml#Fig5).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig5_HTML.jpg)

A screenshot of the H 2 console. S Q L statement is Select star from an employee. The employee details are I D, email, employee identifier, name, and salary.

Figure 2-5

H2 console showing the employee table being updated with the data

This completes the steps for creating, running, and testing the code of a local function.

In the following sections, you’ll see how to use the same code with little to no changes and deploy it to a serverless function container of your choice.

## 2.2 Setting Up Spring Cloud Function and AWS Lambda

This section discusses deploying the code that you created into AWS Lambda.

You have to do a little bit of tweaking to make the code compatible with Lambda. The tweaking is not too disruptive. Again, going back to real situations where you have over a thousand functions you want to move to AWS, this tweak will not be that significant. Of course, it depends on what cloud provider you are starting to create functions for. Ideally, I have seen enterprises gravitate toward Lambda, but there are enterprises that start with Azure or Google. In any case, making these functions portable across clouds requires a few code changes and some `pom.xml` changes.

Prerequisites:

*   AWS account

*   AWS Lambda Function subscription

*   AWS CLI (optional)

*   Code from GitHub at [`https://github.com/banup-kubeforce/payroll-aws`](https://github.com/banup-kubeforce/payroll-aws)

Step 1: First follow Steps 1-6 outlined in Section 2.1\. The code remains the same for any serverless offerings from the cloud providers.

The change introduced at the code level will be in the main Spring Boot class called `PayrollApplication`. You will change this:

```
SpringApplication.run(PayrollApplication.class, args )
```

to this:

```
FunctionalSpringApplication.run(PayrollApplication.class, args)
```

This process is shown in Listing [2-8](#526597_1_En_2_Chapter.xhtml#PC13).

```
package com.kubeforce.payrollaws;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.function.context.FunctionalSpringApplication;
import org.springframework.context.annotation.Bean;
@SpringBootApplication
public class PayrollAwsApplication {
public static void main(String[] args) {SpringApplication.run(PayrollAwsApplication.class,args);
//SpringApplication.run can be used to run the function application locally.
//Change SpringApplication.run to FunctionalSpringApplication.run when deploying to serverless platforms
// such as lambda, knative, etc.
}
@Bean
public EmployeeFunction employeeFunction() {
return new EmployeeFunction();
}
@Bean
public EmployeeConsumer employeeConsumer() {
return new EmployeeConsumer();
}
@Bean
public EmployeeSupplier employeeSupplier() {
return new EmployeeSupplier();
}
}
Listing 2-8
PayrollAwsApplication.java
```

The next step is to change the `pom.xml` file to add the AWS Lambda adapter.

Step 2: Change POM to include AWS dependencies and plugins, as shown in Listing [2-9](#526597_1_En_2_Chapter.xhtml#PC14).

```

4.0.0

org.springframework.boot
spring-boot-starter-parent
2.7.3

com.kubeforce
payroll-aws
0.0.1-SNAPSHOT
payroll-aws
payroll-aws

11
2021.0.1
3.9.0

org.springframework.boot
spring-boot-starter-data-jpa

org.springframework.boot
spring-boot-starter-web

org.springframework.cloud
spring-cloud-function-web

com.amazonaws
aws-lambda-java-core
1.2.1
provided

org.springframework.cloud
spring-cloud-function-adapter-aws

com.amazonaws
aws-lambda-java-events
3.9.0

com.h2database
h2
runtime

org.springdoc
springdoc-openapi-ui
1.6.11

org.springdoc
springdoc-openapi-webflux-ui
1.6.11

org.springframework.boot
spring-boot-starter-test
test

org.springframework.cloud
spring-cloud-dependencies
${spring-cloud.version}
pom
import

org.apache.maven.plugins
maven-deploy-plugin

true

org.springframework.boot
spring-boot-maven-plugin

org.springframework.boot.experimental
spring-boot-thin-layout
1.0.28.RELEASE

org.apache.maven.plugins
maven-shade-plugin
3.2.4

false
true
aws

Listing 2-9
The pom.xml File
```

The POM needs to be changed to accommodate the Spring Cloud Function adapter for AWS, Lambda events, and the Maven shade plugin (optional).

These changes will create a package that can be deployed to AWS Lamdba.

Step 3: Package the application into a JAR file to deploy to AWS Lambda. Figure [2-6](#526597_1_En_2_Chapter.xhtml#Fig6) shows how to trigger a Maven package run in IntelliJ. Note that `payroll-aws-0.0.1-SNAPSHOT-aws.jar` is created in the target folder.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig6_HTML.jpg)

A screenshot of Run Maven. The left side contains project details. The right side contains Maven. Some of them are packages run Maven build, run payroll-a w s, debug payroll-a w s, and profile payroll-a w s.

Figure 2-6

Run Maven:package from the IDE

Step 4: Create a function definition in AWS Lambda.

Log in to AWS and subscribe to AWS Lambda. Information on how to subscribe to Lambda is available at [`https://docs.aws.amazon.com/lambda/latest/dg/getting-started.xhtml`](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.xhtml).

Create a function defined on the Lambda portal/dashboard, as shown in Figure [2-7](#526597_1_En_2_Chapter.xhtml#Fig7).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig8_HTML.jpg)

A screenshot of an employee consumer page. It contains a function overview and employee consumer layers.

Figure 2-8

Upload the JAR file into the AWS Lambda dashboard

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig7_HTML.jpg)

A screenshot of Create function. It contains the author from scratch and uses a blueprint, and container image. Author from scratch contains basic information, permissions, and advanced settings.

Figure 2-7

AWS lambda dashboard to create a function

Upload the JAR file created in Step 3 and provide the settings, as illustrated in Figure [2-7](#526597_1_En_2_Chapter.xhtml#Fig7).

Use the Upload From button, as shown in Figure [2-9](#526597_1_En_2_Chapter.xhtml#Fig9), to access the choices for uploading a file.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig9_HTML.jpg)

A screenshot of the edit runtime setting page. The page contains runtime settings, runtime as Java 11, handler, and architecture.

Figure 2-9

AWS Lambda runtime settings

Set the handler as shown. The Lambda function handler is the method in your function code that processes events. This will indicate that the Spring Cloud Function adapter will be invoked when the function is called. I used Java 11\. Refer to the `pom.xml`.

Once you click Save, you can start your testing process.

Step 5: Test the function. The AWS Portal allows developers to run the tests against the function. Clicking the Test tab will take you to the testing dashboard, as shown in Figure [2-10](#526597_1_En_2_Chapter.xhtml#Fig10).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig10_HTML.jpg)

A screenshot of the function overview page from the test function. The page contains employee consumer layers and descriptions. Last modified details, function A R N, and function U R L.

Figure 2-10

AWS Lamba Functions Test tab

The testing dashboard allows you to send a JSON file as an event to trigger the function.

In the Test Event section, you can provide the event name or choose other settings. You can run the event with just the defaults for test purposes; the only parameter that needs to be provided is the input JSON payload. Click Test when you’re ready, as shown in Figure [2-11](#526597_1_En_2_Chapter.xhtml#Fig11).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig11_HTML.jpg)

A screenshot of the test event page. It contains test event action, event name, event sharing settings as private, and template-optional. Event J S O N contains name, employee identifier, email, and salary.

Figure 2-11

Testing the dasboard

Figure [2-12](#526597_1_En_2_Chapter.xhtml#Fig12) shows you the details of the test. Note that the test is successful.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig12_HTML.jpg)

A screenshot of an execution result of success. The page contains the details of the test, summary, and log output. Summaries of code, request I D, duration.

Figure 2-12

AWS Lambda function test results

## 2.3 Setting Up Spring Cloud Function and Google Cloud Functions

You will use the same example from Section 2.1 and modify it a bit to make it run on Google Cloud Functions. The approach is similar to AWS Lambda, but you will be leveraging Google’s libraries.

Prerequisites:

*   Google account

*   Subscription to Google Cloud Functions

*   Google CLI (this is critical as it is a more efficient way than going through the Google Portal)

*   Code from GitHub at [`https://github.com/banup-kubeforce/payroll-gcp`](https://github.com/banup-kubeforce/payroll-gcp)

Step 1: Follow Steps 1-6 outlined in Section 2.1\. This step is the same as you completed for AWS Lambda. The code is very similar to the AWS code. See Listing [2-10](#526597_1_En_2_Chapter.xhtml#PC15).

```
package com.kubeforce.payrollgcp;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.function.context.FunctionalSpringApplication;
import org.springframework.context.annotation.Bean;
@SpringBootApplication
public class PayrollGcpApplication {
public static void main(String[] args) {
FunctionalSpringApplication.run(PayrollGcpApplication.class, args);
}
@Bean
public EmployeeFunction exampleFunction() {
return new EmployeeFunction();
}
@Bean
public EmployeeConsumer employeeConsumer() {
return new EmployeeConsumer();
}
@Bean
public EmployeeSupplier exampleSupplier() {
return new EmployeeSupplier();
}
}
Listing 2-10
PayrollGcpApplication.java
```

Step 2: Configure POM. The `pom.xml` file needs to be modified to include the Spring Cloud Function adapter and any plugins that are specific to GCP. See Listing [2-11](#526597_1_En_2_Chapter.xhtml#PC16).

You will be adding some GCP function-specific dependencies, such as `spring-cloud-gcp-starter`, `spring-cloud-function-adapter-gcp`, `spring-cloud-gcp-dependencies`, and `spring-cloud-function-adapter-gcp`.

```

4.0.0

org.springframework.boot
spring-boot-starter-parent
2.6.5

com.kubeforce
payroll-gcp
0.0.1-SNAPSHOT
payroll-gcp
payroll-gcp

11
3.1.0
2021.0.1
4.0.0-SNAPSHOT

org.springframework.boot
spring-boot-starter-data-jpa

org.springframework.boot
spring-boot-starter-web

com.google.cloud
spring-cloud-gcp-starter

org.springframework.cloud
spring-cloud-function-web

org.springframework.cloud
spring-cloud-function-adapter-gcp

com.h2database
h2
runtime

org.springframework.boot
spring-boot-starter-test
test

org.springframework.cloud
spring-cloud-dependencies
${spring-cloud.version}
pom
import

com.google.cloud
spring-cloud-gcp-dependencies
${spring-cloud-gcp.version}
pom
import

maven-deploy-plugin

true

org.springframework.boot
spring-boot-maven-plugin

target/deploy

org.springframework.cloud
spring-cloud-function-adapter-gcp
3.2.2

com.google.cloud.functions
function-maven-plugin
0.9.1

org.springframework.cloud.function.adapter.gcp.GcfJarLauncher
8080

Listing 2-11
The pom.xml File
```

Step 3: Build, package, and deploy to Google Cloud Functions. The Google cloud website for this activity is a bit clumsy. You have to zip up your entire directory and upload it. This may not be allowed in some enterprises. I therefore reverted to using the CLI to accomplish this task. See Figure [2-13](#526597_1_En_2_Chapter.xhtml#Fig13).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig13_HTML.jpg)

A screenshot of G cloud C L I execution results. It uses the C L I to accomplish the task. Some of the details on the page contain available memory M b, build I D, build name, docket registry, and entry point.

Figure 2-13

Gcloud CLI execution results

This command must be run from the project root folder:

```
$gcloud functions deploy payroll-gcp --entry-point org.springframework.cloud.function.adapter.gcp.GcfJarLauncher --runtime java11 --trigger-http --source target/deploy --memory 512MB
```

Once the command has been successfully executed, you can go to your Google Cloud Functions console to see the deployed function. See Figure [2-14](#526597_1_En_2_Chapter.xhtml#Fig14).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig14_HTML.jpg)

A screenshot of the Google cloud function dashboard. It contains cloud functions and functions. Some of the details it contains the environment, name, last deployed, region, trigger, runtime, and memory allocated.

Figure 2-14

Cloud Functions dashboard

Now you can test it to verify that it works.

Step 4: Test the function in GCP. Google provides a way to test from the website itself. You can provide the input in the form of the JSON file, as you did before, and then execute the tests.

Figure [2-15](#526597_1_En_2_Chapter.xhtml#Fig15) shows the successful execution of the test.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig15_HTML.jpg)

A set of two screenshots of the cloud functions testing dashboard. a. Google cloud platform described triggering event. b. Cloud functions payroll g c p.

Figure 2-15

Cloud Functions testing dashboard

## 2.4 Setting Up Spring Cloud Function Azure Functions

You will use the same example from Section 2.1 and modify it a bit to make it run on Azure Functions. You will see that the approach for Azure Functions is different from AWS or Google. You have to add another class called `EmployeeConsumerHandler`, which extends a `FunctionInvoker<I,O>`. This `FunctionInvoker` class is merely a pass through for the `EmployeeConsumer` function.

Prerequisites:

*   Azure account

*   Subscription to Azure Functions

*   Azure Functions CLI. You can download the tools at [`https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local`](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)

*   Code from GitHub at [`https://github.com/banup-kubeforce/payroll-azure`](https://github.com/banup-kubeforce/payroll-azure)

Step 1: Follow Steps 1-6 outlined in Section 2.1.

You have to introduce a `FunctionInvoker <I,O>` class, which makes the code non-portable to other cloud providers. While AWS and Google externalize the invocation of the function, as you have seen in previous sections, Azure forces you to provide a handler class that extends a `FunctionInvoker`.

Listing [2-12](#526597_1_En_2_Chapter.xhtml#PC18) introduces the class called `EmployeeConsumerHandler`, which extends `FunctionInvoker`. The input for this `FunctionInvoker` will be a `Map<String,String>`, which will take in the JSON object that is passed. This then gets sent to the `EmployeeConsumer` function, which processes the data.

```
package com.kubeforce.payrollazure;
import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;
import org.aspectj.weaver.NewConstructorTypeMunger;
import org.springframework.cloud.function.adapter.azure.FunctionInvoker;
import java.util.List;
import java.util.Map;
import java.util.Optional;
import org.springframework.messaging.Message;
import org.springframework.messaging.support.MessageBuilder;
public class EmployeeConsumerHandler extends FunctionInvoker,String  >{
@FunctionName("employeeConsumer")
public String execute(
@HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage> request,
ExecutionContext context) {
String message = "Successfully inserted: " +request.getBody().get().getName();
return message;
}
}
Listing 2-12
EmployeeConsumerHandler.java
```

Step 2: Build and package the function. IntelliJ offers you the ability to execute Maven commands through a Maven window, as shown in Figure [2-16](#526597_1_En_2_Chapter.xhtml#Fig16).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig16_HTML.png)

A screenshot of the Maven package. The left side contains coding. The right side contains commands. Run payroll Azure package command is selected.

Figure 2-16

The Maven package

You have to run the `maven clean` command before you run the `maven package` command. These commands are under the Lifecycle menu in the Maven window. Run the Maven package to get the deployable JAR file. These JAR files are stored in the target folder in your project directory

Figure [2-17](#526597_1_En_2_Chapter.xhtml#Fig17) shows a successful run of the Maven package.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig17_HTML.jpg)

A screenshot of a successful package. It contains payroll Azure package logs.

Figure 2-17

Azure Functions build and succcessful package

Step 3: Deploy the function in Azure. You will see some artifacts created in the target folder. `Payroll-azure-0.0.1-SNAPSHOT.jar` is the file you are interested in. This file needs to be deployed to the Azure Cloud.

IntelliJ has a feature/plugin for Azure that you can enable. Once you enable that feature, you can perform Azure Function-based activities from the IDE.

In this case, to start the deploy process, choose Run Azure Functions ➤ Deploy from the Azure Functions list, as shown in Figure [2-18](#526597_1_En_2_Chapter.xhtml#Fig18).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig18_HTML.jpg)

A screenshot of payroll-azure. The left side contains projects. Target is selected. In coding payroll, a function is selected. The right side contains the commands. Azure-function deploy is selected.

Figure 2-18

Use Azure Functions, Deploy in Maven to deploy to Azure

After the function is successfully deployed to the Azure Cloud, you can use the URL provided to runs tests in Postman or proceed to the Azure Functions console to test.

The Azure Functions console is shown in Figure [2-19](#526597_1_En_2_Chapter.xhtml#Fig19).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig19_HTML.jpg)

A screenshot of the function application. A payroll-azure function is selected. In the payroll-azure function, the function tag is selected, and it contains the name, trigger, and status.

Figure 2-19

Azure Functions Console

You will see that the function is deployed in Azure and is running.

Step 4: Test. Clicking the `employeeConsumer` function will take you to the detail console, as shown in Figure [2-20](#526597_1_En_2_Chapter.xhtml#Fig20). Here, you can conduct tests by clicking the Code + Test link. The dashboard has an Input section, where you can specify the JSON in the body, choose the HTTP method as POST (in this example), and click the Run button. You can see the results of the execution in the command console, as shown in Figure [2-21](#526597_1_En_2_Chapter.xhtml#Fig21).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig22_HTML.jpg)

A screenshot of the execution log. It contains creating cluster k native. Some of the checks are to ensure node image, prepare nodes, and writing configuration. The output is you can now use your cluster.

Figure 2-22

Cluster has been successfully created

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig21_HTML.png)

A screenshot of employee consumer code plus test page. Code plus test tag is selected. The output is the H T T P response code, successful H T T P response content. It contains the run logs.

Figure 2-21

Successful run of the test

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig20_HTML.png)

A screenshot of employee consumer code plus test page. The test tag is selected. In payroll-azure, employee consumer input is the H T T P method, key, query, headers, and body. It contains a run page.

Figure 2-20

Run test with input

Figure [2-21](#526597_1_En_2_Chapter.xhtml#Fig21) shows successful execution of the test.

## 2.5 Setting Up Locally with Kubernetes and Knative and Spring Cloud Function

This section explores another way to deploy Spring Cloud Function. Kubernetes, as you might be aware, is a portable approach to containerization and running containers. Kubernetes allows you to host containers that are packaged as images and deployed on Kubernetes pods.

Knative is an environment that sits on Kubernetes and can host a function. Chapter [1](#526597_1_En_1_Chapter.xhtml) explained how the cloud providers created environments such as AWS Lambda that could start up on demand and shut down when a resource is not in use. Similarly, you can create such an on-demand environment with Knative for functions. This section explores how to set up the Knative environment locally, which is difficult with AWS and Google Functions, and deploy and work with your functions locally and on the cloud. This section starts with local deployment and then moves on to the cloud.

You will notice that I used `EmployeeSupplier` as the function. To do this, you need to prepopulate the H2 database with some data so that you can query the database using the `EmployeeSupplier` function.

Prerequisites:

*   Docker on Mac, Linux, or Windows

*   Kind (Kubernetes in Docker)

*   Knative serving

*   IntelliJ, Eclipse, VS Code, or other IDEs

*   Code from GitHub

Here is the process of setting up Kubernetes and Knative and deploying Spring Cloud Function.

Step 1: Set up a Kubernetes cluster locally. In this step, you install a local Kubernetes cluster. You deploy a KIND (Kubernetes IN Docker). Cluster. Of course, you can use Minikube or Minishift to deploy locally.

First, you need Docker.

1.  Install Docker.

I am using a Ubuntu VM since it is a popular Linux offering. The command lines are provided in sequence, so you can follow the process. See Listing [2-13](#526597_1_En_2_Chapter.xhtml#PC19).

```
$sudo apt-get update
$sudo apt-get install -y \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common
$curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
$sudo apt-get update
$sudo apt-get install -y docker-ce docker-ce-cli containerd.io
$sudo usermod -aG docker $USER
Listing 2-13
Install Docker
```

Once you run these commands, you will have a running Docker instance.

1.  Install Kubectl.

    This step is required, as you need Kubectl to interact with the Kubernetes cluster

```
$sudo curl -L "https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl" -o /usr/local/bin/kubectl
```

Run `chmod` to make Kubectl executable:

1.  Install KIND.

```
$sudo chmod +x /usr/local/bin/kubectl
```

```
$sudo curl -L "https://kind.sigs.k8s.io/dl/v0.8.1/kind-$(uname)-amd64" -o /usr/local/bin/kind
```

Run `chmod` to make KIND executable:

```
$sudo chmod +x /usr/local/bin/kind
```

Now you have the KIND bits that will allow you to install the cluster and deploy Knative.

Step 2: Configure Knative. In order to configure Knative, you need a KIND cluster. You will create a cluster with custom configuration.

Create a cluster using a configuration file called `clusterconfig.yaml`. Note that the name of the cluster is "`knative`”. You can name it differently, but you have to use that cluster to deploy Knative; see Listing [2-14](#526597_1_En_2_Chapter.xhtml#PC24).

```
$cat > clusterconfig.yaml << EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
extraPortMappings:
## expose port 31380 of the node to port 80 on the host
- containerPort: 31080
hostPort: 80
## expose port 31443 of the node to port 443 on the host
- containerPort: 31443
hostPort: 443
EOF
$kind create cluster --name knative --config clusterconfig.yaml
Listing 2-14
Create clusterconfig.yaml and Run the Create Cluster
```

To set up Knative, you need to install and configure the following:

*   Knative serving (information about Knative serving is available at [`https://knative.dev/docs/serving`](https://knative.dev/docs/serving))

*   Kourier is a lightweight Knative serving ingress and is available at [`https://github.com/knative-sandbox/net-kourier`](https://github.com/knative-sandbox/net-kourier).

*   Magic DNS is a DNS provider and is available at [`https://knative.dev/docs/install/yaml-install/serving/install-serving-with-yaml/#configure-dns`](https://knative.dev/docs/install/yaml-install/serving/install-serving-with-yaml/%2523configure-dns).

Install Knative serving

1.  Install the Knative serving components (i.e., `crds` and `core`):

1.  Set up networking using Kourier:

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.6.0/serving-crds.yaml
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.6.0/serving-core.yaml
```

1.  Change the file to use nodePort, as shown in Listing [2-15](#526597_1_En_2_Chapter.xhtml#PC27).

```
$curl -Lo kourier.yaml https://github.com/knative/net-kourier/releases/download/knative-v1.6.0/kourier.yaml
```

1.  Install Kourier.

```
apiVersion: v1
kind: Service
metadata:
name: kourier
namespace: kourier-system
labels:
networking.knative.dev/ingress-provider: kourier
spec:
ports:
- name: http2
port: 80
protocol: TCP
targetPort: 8080
nodePort: 31080
- name: https
port: 443
protocol: TCP
targetPort: 8443
nodePort: 31443
selector:
app: 3scale-kourier-gateway
type: NodePort
Listing 2-15
Configure nodePort
```

Install Kourier by running the following command (see Figure [2-23](#526597_1_En_2_Chapter.xhtml#Fig23)):

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig23_HTML.jpg)

A screenshot of the execution log. It includes configuration, service account, cluster role, deployment, and service of kourier. The output is service kourier-internal created.

Figure 2-23

Kourier is installed successfully

```
$ kubectl apply --filename kourier.yaml
```

1.  Set Kourier as the default networking layer by installing the following patch:

1.  Set up a wildcard DNS with `sslip.io`:

```
$ kubectl patch configmap/config-network \
--namespace knative-serving \
--type merge \
--patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
```

1.  Patch with `sslip.io`:

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.6.0/serving-default-domain.yaml
```

1.  Get the status of the pods in the Knative serving:

```
$ kubectl patch configmap/config-domain \
--namespace knative-serving \
--type merge \
--patch '{"data":{"127.0.0.1.sslip.io":""}}'
```

```
$ kubectl get pods -n knative-serving
```

You can see in Figure [2-24](#526597_1_En_2_Chapter.xhtml#Fig24) that all the components are up and running. You can go to the next step of publishing your Spring Cloud Function app on Knative.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig24_HTML.jpg)

A screenshot of the run log. It includes Kubert l get pods k native serving name, an activator, autoscaler, default domain, and domain mapping.

Figure 2-24

Knative services pods are up and running

Step 3: Containerize the app and push it to a repository. Create a Dockerfile with the parameters set shown in Listing [2-16](#526597_1_En_2_Chapter.xhtml#PC33). I used jdk8.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig25_HTML.jpg)

A screenshot of the docker file. The left side contains the project. A docker file is selected. Push image page created. It contains a registry, namespace, repository, and tag.

Figure 2-25

Docker Push from IntellJ

1.  Push to Dockerhub. You can use the IDEs Docker features to push it to the Docker registry, as shown in Figure [2-25](#526597_1_En_2_Chapter.xhtml#Fig25).

```
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
Listing 2-16
The Dockerfile
```

Step 4: Deploy the app to Knative. You need to create a YAML file for the service. Notice the image that has been used. I pushed a Docker image called “`main`” that exposes `employeeSupplier`. You will see that I will use a different image when pushing to other cloud providers. This is to get you acquainted with pushing different images with different exposed endpoints. See Listing [2-17](#526597_1_En_2_Chapter.xhtml#PC34).

1.  The next step is to deploy the app to Knative.

```
apiVersion: serving.knative.dev/v1 # Current version of Knative
kind: Service
metadata:
name: payroll # The name of the app
namespace: default # The namespace the app will use
spec:
template:
spec:
containers:
- image: docker.io/banupkubeforce/springcloudfunctions:main # The URL to the image of the app
env:
- name: TARGET # The environment variable printed out by the sample app
value: "employeesupplier"
Listing 2-17
payroll.yaml
```

Run the following command to install the application with Knative serving:

```
$ kubectl apply -f payoll.yaml
```

Alternatively, you can use a Knative CLI. More information can be found at [`https://knative.dev/docs/client/#kubectl`](https://knative.dev/docs/client/%2523kubectl)`.`

```
$ kn service create payroll –image docker.io/banupkubeforce/springcloudfunctions:main
```

A YAML execution gives you more control over the target environment. Note this URL, as it is required for the testing step. The URL shown here for example is [`https://payroll.default.127.0.0.1.sslip.io`](https://payroll.default.127.0.0.1.sslip.io).

You can run the following command to get the URL and check if the endpoint is ready for testing.

Step 5: Test . Since `employeeSupplier` queries the database and gets the records, you need to use a `GET` operation. See Figure [2-26](#526597_1_En_2_Chapter.xhtml#Fig26).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig26_HTML.png)

A screenshot of the test with the postman. The authorization tag is selected and the body contains I D, name, email, salary, and employee identifier.

Figure 2-26

Test with Postman

In this section, you created a KIND-based Kubernetes cluster, configured Knative, and deployed the application. You created a portable image that can be deployed to any Kubernetes cluster that has been configured with Knative.

## 2.6 Setting Up AWS with EKS and Knative with Spring Cloud Function

This section looks at deploying to an AWS EKS cluster that has Knative configured. You will see similarities to Section 2.5, because you create the cluster, enable Knative, and deploy a Docker image.

Prerequisites:

*   AWS subscription ([`https://aws.amazon.com/`](https://aws.amazon.com/))

*   AWS CLI, which can be found at [`https://aws.amazon.com/cli/`](https://aws.amazon.com/cli/)

*   Eksctl CLI ([`https://eksctl.io/`](https://eksctl.io/))

*   Knative serving ([`https://knative.dev/docs/serving`](https://knative.dev/docs/serving))

*   Knative CLI ([`https://knative.dev/docs/client/install-kn/`](https://knative.dev/docs/client/install-kn/))

*   IntelliJ, Eclipse, VS Code, or other IDEs

*   Code from GitHub

*   Docker image registered in the Docker hub

Step 1: Set up a Kubernetes cluster with EKS. Before you run the command in Listing [2-18](#526597_1_En_2_Chapter.xhtml#PC37), ensure that you are properly connected to AWS and have the subscriptions and permissions to create a cluster. Additional information can be found at [`https://docs.aws.amazon.com/eks/latest/userguide/getting-started.xhtml`](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.xhtml).

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
name: payroll-clstr
region: us-east-2
nodeGroups:
- name: payroll-nodes
instanceType: m5.large
desiredCapacity: 3
volumeSize: 80
Listing 2-18
cluster.yaml
```

Create your Amazon EKS cluster using the following command:

```
$eksctl create cluster -f cluster.yaml
```

The process takes 10 to 15 minutes. At the end of the process, you will have created a cluster in EKS. You can verify it in the AWS EKS console in the cloud. See Figure [2-27](#526597_1_En_2_Chapter.xhtml#Fig27).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig27_HTML.jpg)

A screenshot of amazon elastic Kubernetes service. The payroll cluster contains an overview and its node's details. They are node name, instance type, node group, created, and status.

Figure 2-27

EKS console

You will see that the dashboard shows the name of the cluster you provided in the Listing [2-18](#526597_1_En_2_Chapter.xhtml#PC37) and the creation of three nodes as set for `desiredCapacity` in the listing.

Verify that the clusters are up and running (see Figure [2-28](#526597_1_En_2_Chapter.xhtml#Fig28)). You can do that by running the following command:

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig29_HTML.jpg)

A screenshot of K native components running logs. It contains name, ready, status, restarts, and age. All namespaces were running status.

Figure 2-29

Knative components running on EKS

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig28_HTML.jpg)

A screenshot of elastic Kubernetes service running logs. It contains Kubect l get pods all namespaces, name, ready, status, restarts, and age. All namespaces were running successfully.

Figure 2-28

EKS cluster running successfully

```
$kubectl get pods --all-namespaces
```

Step 2: Configure Knative on EKS. In this step, you configure Knative on the EKS cluster you created. See Listing [2-19](#526597_1_En_2_Chapter.xhtml#PC40).

```
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-crds.yaml
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-core.yaml
$kubectl apply -f https://github.com/knative/net-kourier/releases/download/knative-v1.4.0/kourier.yaml
$kubectl patch configmap/config-network \
--namespace knative-serving \
--type merge \
--patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
$kubectl --namespace kourier-system get service kourier
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-default-domain.yaml
Listing 2-19
Install and Configure Knative
```

Step 3: Containerize the app with Docker and push it to a repository (optional).

This is an optional step, as you already deployed an image in Section 2.5\. You can skip this step and go to Step 4\. I used JDK 8 here, but you can use the latest JDK by changing the `FROM` statement to `FROM adoptopenjdk/openjdk11:latest`. See Listing [2-20](#526597_1_En_2_Chapter.xhtml#PC41).

```
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
Listing 2-20
The Dockerfile
```

Step 4: Push to the Docker hub. The next step is to push the Docker image to the Dockerhub repository, as shown in Figure [2-30](#526597_1_En_2_Chapter.xhtml#Fig30). Be sure to log in to Dockerhub at [`https://hub.docker.com/`](https://hub.docker.com/) and create a repository and namespace. You will need it for the Docker push.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig30_HTML.jpg)

A screenshot of payroll. The left side contains the project. In that, a docker file is selected. The push image page is created. It includes a registry, namespace, repository, and tag.

Figure 2-30

Docker push from the IntelliJ IDE

Once the push is successful, you can navigate to Dockerhub to check for the deployment of the image, as shown in Figure [2-31](#526597_1_En_2_Chapter.xhtml#Fig31).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig31_HTML.jpg)

A screenshot of the deploy image of the docker hub. The general page contains spring cloud functions, docker commands, tags and scans, and automated builds.

Figure 2-31

Dockerhub with the deployed image

Step 5: Deploy the app to Knative on EKS. In this step, you create a YAML file and set the location of the image that you will deploy to Knative.

Create the `payroll.yaml` file and set up the image to be deployed, as shown in Listing [2-21](#526597_1_En_2_Chapter.xhtml#PC42).

```
apiVersion: service.knative.dev/v1
kind: service
metadata:
name: payroll
spec:
template:
spec:
containers:
- image: docker.io/banupkubeforce/springcloudfunctions/payrollv2
ports:
- containerPort: 80
env:
- name: TARGET
value: "employeeconsumer"
Listing 2-21
The payroll.yaml File
```

Deploy the app to Knative on EKS.

Run the following command:

```
$ kubectl apply -f payoll.yaml
```

Alternatively, you can use a Knative CLI. More information can be found at [`https://knative.dev/docs/client/#kubectl`](https://knative.dev/docs/client/%2523kubectl).

```
$ kn service create payroll –image docker.io/banupkubeforce/springcloudfunctions:payrollv2
```

Figure [2-32](#526597_1_En_2_Chapter.xhtml#Fig32) shows the result of running the `kn cli`. A YAML execution gives you more control over the target environment. Note the URL, as it is required for the testing step. The URL shown here for example is [`https://payroll.default.13.58.221.247.sslip.io`](https://payroll.default.13.58.221.247.sslip.io).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig32_HTML.jpg)

A screenshot of payroll deployment logs. The run commands are route is still working to reflect, configuration payroll is waiting, waiting for a load balancer to be ready, and ready to serve.

Figure 2-32

Payroll is deployed successfully

You can run the following command to get the URL and check if the endpoint is ready for testing:

```
$ kn services list
```

The URL that you need for testing or posting is `payroll.default.13.58.221.247.sslip.io`

Step 6: Testing. Use the URL you got in Step 5 and run the test. The URL in this case is [`https://payroll.default.13.58.221.247.sslip.io/employeeConsumer`](https://payroll.default.13.58.221.247.sslip.io/employeeConsumer)

You can use Postman or curl to run the tests. Figure [2-33](#526597_1_En_2_Chapter.xhtml#Fig33) shows the Postman test results.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig34_HTML.jpg)

A screenshot of cluster running logs. It contains the name, location, master version, master I P, machine type, node version, number of nodes, and status.

Figure 2-34

Cluster is up and running

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig33_HTML.jpg)

A screenshot of the postman page. The header tags are home, workspace, A P I network, reports, and explore. The body contains the name, employee identifier, email, and salary.

Figure 2-33

Test Is Postman with the URL

## 2.7 Setting Up GCP with Cloud Run/GKE and Knative with Spring Cloud Function

This section looks at deploying to GCP a GKE cluster that has Knative configured. You will see similarities to Section 2.5, because you create the cluster, enable Knative, and deploy a Docker image.

Prerequisites:

*   GCP subscription ([`https://cloud.google.com/`](https://cloud.google.com/))

*   The Gcloud CLI, which can be found at [`https://cloud.google.com/sdk/docs/install`](https://cloud.google.com/sdk/docs/install)

*   kubectl ([`https://kubernetes.io/docs/tasks/tools/`](https://kubernetes.io/docs/tasks/tools/))

*   Knative serving ([`https://knative.dev/docs/serving`](https://knative.dev/docs/serving))

*   Knative CLI ([`https://knative.dev/docs/client/install-kn/`](https://knative.dev/docs/client/install-kn/))

*   IntelliJ, Eclipse, VS Code, or other IDEs

*   Code from GitHub (optional if you use the image from Dockerhub)

*   Docker image registered in the Docker hub

Step 1: Set up a Kubernetes cluster with GKE. In this step, you create a GKE cluster. Make sure you have the sufficient permissions to create the cluster. Additional information can be found at [`https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster`](https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster).

Before running the command ensure, that you have logged in to GCP using the Gcloud CLI. Run the command in Listing [2-22](#526597_1_En_2_Chapter.xhtml#PC46).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig35_HTML.jpg)

A screenshot of the status of the G K E cluster. It contains the name, status, roles, age, and version. All the statuses were ready.

Figure 2-35

Status of GKE cluster

```
$gcloud container clusters create payroll-clstr \
--zone=us-central1-a \
--cluster-version=latest \
--machine-type=n1-standard-4 \
--enable-autoscaling --min-nodes=1 --max-nodes=10 \
--enable-autorepair \
--scopes=service-control,service-management,compute-rw,storage-ro,cloud-platform,logging-write,monitoring-write,pubsub,datastore \
--num-nodes=3
Listing 2-22
Create a Cluster in GCP
```

Check the status of the cluster by running the following command:

```
$kubectl get nodes
```

Navigate to Google Clouds GKE dashboard to verify the cluster. See Figure [2-36](#526597_1_En_2_Chapter.xhtml#Fig36).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig37_HTML.jpg)

A screenshot of Kourier running logs. It contains namespace, type, and patch. The network was successfully dispatched.

Figure 2-37

Kourier has been successfully patched

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig36_HTML.jpg)

A screenshot of the Google Kubernetes engine. Cluster tag selected. It contains Kubernetes clusters. Its overview contains status, name, location, number of nodes, total v C P Us, and total memory.

Figure 2-36

GKE Dashboard on Google Cloud

Step 2: Configure Knative on GKE. This step is similar to what you did earlier with EKS or local Kubernetes:

1.  Install the Knative serving components by running the following commands:

1.  Install the Kourier components required for ingress by running the following command:

```
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-crds.yaml
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-core.yaml
```

1.  Configure Kourier for ingress with the following command:

```
$kubectl apply -f https://github.com/knative/net-kourier/releases/download/knative-v1.4.0/kourier.yaml
```

```
$kubectl patch configmap/config-network \
--namespace knative-serving \
--type merge \
--patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
```

1.  Apply the default domain using the following command:

1.  Check the status of the Knative components by running the following command:

```
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-default-domain.yaml
```

```
$kubectl get pods -n knative-serving
```

1.  Run the following command to get the Kourier ingress information:

```
$kubectl –namespace kourier-system get service kourier
```

Now that you have Knative configured and running, you can proceed to the next step of pushing the app image to Knative.

Step 3: Containerize the app with Docker and push it to a repository (optional).

This is an optional step, as you already deployed an image in Section 2.5\. You can skip this step and go to Step 4\. I used JDK 8 here, but you can use the latest JDK by changing the `FROM` statement to `FROM adoptopenjdk/openjdk11:latest`, as shown in Listing [2-23](#526597_1_En_2_Chapter.xhtml#PC54).

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig38_HTML.jpg)

A screenshot of K native serving running logs. It contains name, ready, status, restarts, and age. All namespaces were running successfully.

Figure 2-38

Knative services are running successfully

```
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
Listing 2-23
The Dockerfile that Helps Create the Container and Image
```

Step 4: Push to the Docker hub. The next step is to push the Docker image to the Dockerhub repository, as shown in Figure [2-40](#526597_1_En_2_Chapter.xhtml#Fig40). Make sure to log in to Dockerhub at [`https://hub.docker.com/`](https://hub.docker.com/) and create a repository and namespace. You will need it for the Docker push.

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig50_HTML.jpg)

A screenshot of application deployment on T K G with K native logs. The commands are route still working, and ready to serve. The output is the service payroll created to the latest revision payroll.

Figure 2-50

App deployment on TKG with Knative

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig49_HTML.jpg)

A screenshot of install of the T K G cluster. It contains the core package repo status. the output is cluster created.

Figure 2-49

Successful install of TKG Cluster

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig48_HTML.jpg)

A screenshot of the installation of T K G bits on a local machine running logs. Installing builder, cluster, Kubernetes release, login, and management cluster. The output is installation completed.

Figure 2-48

Successful installation of TKG bits on a local machine

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig47_HTML.jpg)

A screenshot of testing of the payroll on A K S with K native. The collections tag was selected and displayed create collection tag. The body contains the name, employee identifier, email, and salary.

Figure 2-47

Successful test of the payroll example on AKS with Knative

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig46_HTML.jpg)

A screenshot of the application deployed on A K S with K native logs. The commands are route is still working, and ready to serve. Output is service payroll created to the latest revision.

Figure 2-46

The app is successfully deployed on AKS with Knative

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig45_HTML.jpg)

A screenshot of Kourier ingress patched running logs. It contains namespace, type merge, and patch. The output-configured network is patched.

Figure 2-45

Kourier ingress is patched

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig44_HTML.jpg)

A screenshot of A K S cluster running logs. It contains names, statuses, roles, and ages. All the clusters were in ready status.

Figure 2-44

The AKS Cluster is ready

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig43_HTML.jpg)

A screenshot of the successful testing with the postman. The header tags are home, workspaces, A P I network, reports, and explore. The body contains the name, employee identifier, email, and salary.

Figure 2-43

Successful testing with Postman

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig42_HTML.jpg)

A screenshot of payroll application run logs. The commands are route is still working, configuration payroll is waiting, and ready to serve. Output is service payroll created to the latest revision.

Figure 2-42

Payroll app deployed on GKE with Knative

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig41_HTML.jpg)

A screenshot of the deployment image of the docker hub. It contains spring cloud functions, docker commands, tags and scans, and automated builds.

Figure 2-41

Dockerhub with the deployed image

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig40_HTML.jpg)

A screenshot of payroll. The left side contains the project. In that, a docker file is selected. The push image page is created. It includes a registry, namespace, repository, and tag.

Figure 2-40

Docker push from the IDE

![](images/526597_1_En_2_Chapter/526597_1_En_2_Fig39_HTML.jpg)

A screenshot of Kourier service status run logs. It contains name, type, cluster I P, external I P, ports, and age.

Figure 2-39

Kourier service status shows an external IP

Once the push is successful, you can navigate to Dockerhub to check for the deployment of the image, as shown in Figure [2-41](#526597_1_En_2_Chapter.xhtml#Fig41).

Step 5: Deploy the app to Knative on GKE. I skip the Kubectl-based deploy method used in Section 2.6, Step 4, and use Knative CLI to deploy the image. If you need more control over the deployment, you can use the method suggested in Section 2.6, Step 4.

```
$ kn service create payroll –image docker.io/banupkubeforce/springcloudfunctions:payrollv2
```

Figure [2-42](#526597_1_En_2_Chapter.xhtml#Fig42) shows the results of running the `kn cli`. A YAML execution gives you more control over the target environment. Note the URL, as it is required for the testing step. The URL shown here for example is [`https://payroll.default.34.69.156.24.sslip.io`](https://payroll.default.34.69.156.24.sslip.io).

Step 6: Test. Use the DNS name provided in Step 5 to test against the following URL:

[`https://payroll.default.34.69.156.24.sslip.io/employeeConsumer`](https://payroll.default.34.69.156.24.sslip.io/employeeConsumer)

Now you have successfully deployed the payroll app on GKE with Knative.

## 2.8 Setting Up Azure with AKS and Knative with Spring Cloud Function

This section looks at deploying to an Azure AKS cluster that has Knative configured. You will see similarities to Section 2.5, because you create the cluster, enable Knative, and deploy a Docker image.

Prerequisites:

*   Azure subscription ([`https://portal.azure.com`](https://portal.azure.com)`/`)

*   Azure CLI, which can be found at [`https://docs.microsoft.com/en-us/cli/azure/install-azure-cli`](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

*   kubectl ([`https://kubernetes.io/docs/tasks/tools/`](https://kubernetes.io/docs/tasks/tools/))

*   Knative serving ([`https://knative.dev/docs/serving`](https://knative.dev/docs/serving))

*   Knative CLI ([`https://knative.dev/docs/client/install-kn/`](https://knative.dev/docs/client/install-kn/)`)`

*   IntelliJ, Eclipse, VS Code, or other IDEs

*   Code from GitHub (optional if you just use the image from Dockerhub)

*   Docker image registered in Docker hub

Step 1: Set up a Kubernetes cluster with AKS. In this step, you create an AKS cluster. Make sure you have the sufficient permissions to create the cluster. Additional information can be found at [`https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli`](https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli).

Before running the following command, ensure that you have logged in to Azure using the Azure CLI. Then run the command in Listing [2-24](#526597_1_En_2_Chapter.xhtml#PC56).

```
az aks create --resource-group $RESOURCE_GROUP \
--name $CLUSTER_NAME \
--generate-ssh-keys \
--kubernetes-version 1.22.6 \
--enable-rbac \
--node-vm-size Standard_DS3_v2
Listing 2-24
The Dockerfile
```

Configure your `kubeconfig` to use the AKS cluster that you created by running the command in Listing [2-25](#526597_1_En_2_Chapter.xhtml#PC57).

```
$ az aks get-credentials --resource-group payroll-rsg --name payrollclstr
Listing 2-25
Set up Kubeconfig
```

Check the status of the cluster by running the command in Listing [2-26](#526597_1_En_2_Chapter.xhtml#PC58).

```
kubectl get nodes
Listing 2-26
Check the Status
```

Step 2: Configure Knative on AKS. This step is similar to what you did earlier with EKS or local Kubernetes.

Install Knative serving:

```
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-crds.yaml
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-core.yaml
$kubectl apply -f https://github.com/knative/net-kourier/releases/download/knative-v1.4.0/kourier.yaml
```

Configure Kourier for ingress with the following command:

```
$kubectl patch configmap/config-network \
--namespace knative-serving \
--type merge \
--patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
```

Apply the default domain using the following command:

```
$kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.4.0/serving-default-domain.yaml
```

Check the status of the Knative components by running the following command:

```
$kubectl get pods -n knative-serving
```

Run the command in Listing [2-27](#526597_1_En_2_Chapter.xhtml#PC63) to get the Kourier ingress information.

```
$kubectl –namespace kourier-system get service kourier
Listing 2-27
Get the Ingress Information
```

Now that you have the Knative configured and running, you can proceed to the next step of pushing the app image to Knative.

Step 3: Containerize the app with Docker and push it to a repository. Follow Step 3 in Sections 2.5, 2.6, or 2.7 if you choose to create and deploy an image to the Dockerhub repository.

Step 4: Deploy the app to Knative on AKS. Here again, I use the Knative CLI `kn` to deploy the app, as it convenient for this simple exercise.

Run the command in Listing [2-28](#526597_1_En_2_Chapter.xhtml#PC64).

```
$ kn service create payroll –image docker.io/banupkubeforce/springcloudfunctions:payrollv2
Listing 2-28
Deploy Payroll Apps
```

Note the URL that is generated. You will use this URL for testing.

Step 5: Test. Use the DNS name provided in Step 4 to test against the following URL:

[`https://payroll.default.20.121.248.sslip.io/employeeConsumer`](https://payroll.default.20.121.248.sslip.io/employeeConsumer)

## 2.9 Setting Up VMware Tanzu TKG and Knative

As with other Kubernetes offerings, setting up and installing Knative on TKG on your local laptop is pretty straightforward.

VMware released a community edition that can be locally installed. Additional information can be found at [`https://tanzucommunityedition.io/download/`](https://tanzucommunityedition.io/download/).

Prerequisites:

*   TKG download ([`https://tanzucommunityedition.io/download/`](https://tanzucommunityedition.io/download/))

*   kubectl ([`https://kubernetes.io/docs/tasks/tools/`](https://kubernetes.io/docs/tasks/tools/))

*   Knative serving ([`https://knative.dev/docs/serving`](https://knative.dev/docs/serving))

*   Knative CLI (`https://knative.dev/docs/client/install-kn/`)

*   IntelliJ, Eclipse, VS Code, or other IDEs

*   Code from GitHub (optional if you just use the image from the Dockerhub)

*   Docker image registered in the Docker hub

Here are the steps to configure and deploy Spring Cloud Function on VMware Tanzu:

*   Step1: Set up TKG locally.

*   Step2: Create a Kubernetes Cluster for your app.

*   Step 3: Install Knative on TKG cluster.

*   Step 4: Deploy the app on Knative.

Step 1: Set up TKG locally. Follow the instructions provided on the website to set up TKG locally. This will give you an unmanaged cluster.

Figure [2-48](#526597_1_En_2_Chapter.xhtml#Fig48) shows a successful install.

Step 2: Create a cluster for your app. Here you create a cluster for the payroll example. Run the following command:

```
$tanzu unmanaged-cluster create payroll
```

You will get a message that the payroll cluster has been completed, as shown in Figure [2-49](#526597_1_En_2_Chapter.xhtml#Fig49).

Step 3: Install Knative on the TKG cluster. Run the following command to install `kantive-serving` on the TKG cluster:

```
$tanzu package install knative-serving --package-name knative-serving.community.tanzu.vmware.com --version 1.0.0
```

Step 4: Deploy the app on Knative, as shown in Figure [2-50](#526597_1_En_2_Chapter.xhtml#Fig50).

Note the URL provided. In this case, it is `http://payroll.default.127.0.0.241.nip.io`.

Step 5: Test Follow Section 2.5, Step 5 and use Postman or curl to test against the [`http://payroll.default.127.0.0.241.nip.io/employeeConsumer`](http://payroll.default.127.0.0.241.nip.io/employeeConsumer) URL.

This completes the successful deployment of Tanzu Kubernetes Grid or TKG.

## 2.10 Summary

This chapter explained how Spring Cloud Function can be developed and deployed to various target platforms, including AWS Lambda, Google Functions, Azure Functions, and Kubernetes environments such as AWS EKS, Google’s GKE, Azure AKS, VMware Tanzu, and the like. With Kubernetes, you were able to use Knative to deploy the same image to all the other Kubernetes flavors without changing the code. Knative ensures that the code is portable across any Kubernetes platform. This is critical, as it ensures movement to any cloud with minimal disruptions. To summarize, you should now have a good understanding of how to build serverless functions with Spring Cloud Function. The next chapter looks at how to automate the deployment process for Spring Cloud Function.

# 3. CI/CD with Spring Cloud Function

As you learned in Chapter [2](#526597_1_En_2_Chapter.xhtml), you can build a Spring Cloud Function and deploy it to multiple environments. You can use various manual methods such as Azure-Function:Deploy, Gcloud CLI, AWS CLI, Kubectl, and Knative CLI. These manual approaches are not sustainable in an enterprise with many different teams, a lot of programmers, and a lot of code. It will be a management nightmare if every team member uses their own method to build and deploy code. Also, as you can see, this process is repeatable. Since it is a repeatable process, there is a chance to leverage automation.

This chapter explores ways to automate the deployment process. It leverages some popular approaches for automating your deploys. It explores GitHub Actions for deploying Lambda, Google Cloud Functions, and Azure Functions and you will integrate with ArgoCD to push to a Kubernetes/Knative environment. While you can use GitHub Actions alone for all environments, that would require custom scripting to push to Kubernetes. ArgoCD has built-in hooks to deploy to Kubernetes, which is the preferred way. More information on GitHub Actions can be found at [`https://github.com/features/actions`](https://github.com/features/actions), and information on ArgoCD can be found at [`https://argoproj.github.io/cd/`](https://argoproj.github.io/cd/).

Let’s dig a bit deeper into GitHub Actions and ArgoCD.

## 3.1 GitHub Actions

This is a CI/CD platform tightly integrated with GitHub; it allows you to create and trigger workflows from GitHub. So, if you are a fan of GitHub, you will really like this new feature. When you sign up for GitHub, GitHub Actions will automatically be integrated into your project, so you do not have to use a separate tool like Jenkins or Circle CI. Of course, this means that you are restricted to GitHub as your code repository. Creating a workflow is quite straightforward. You can create a workflow directly on the GitHub website by navigating to your project and clicking the New Workflow button in the Actions tab, as shown in the Figure [3-1](#526597_1_En_3_Chapter.xhtml#Fig1).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig1_HTML.jpg)

A screenshot of the banup-kubeforce window. Tab action is selected. It describes all workflows. It contains runs from all workflows, events, statuses, brands, and actors.

Figure 3-1

Creating a new GitHub Actions workflow

Upon clicking New Workflow, as shown in Figure [3-2](#526597_1_En_3_Chapter.xhtml#Fig2), you will be taken to the workflow “marketplace,” where you can choose from the suggested flows or set up a workflow yourself. Click the Set Up a Workflow Yourself link to start creating a custom workflow.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig2_HTML.png)

A screenshot of the banup-kubeforce window. Tab actions is selected which displays a message to choose a workflow.

Figure 3-2

Workflow marketplace to choose your workflow setup

The Set Up a Workflow Yourself window, as shown in Figure [3-3](#526597_1_En_3_Chapter.xhtml#Fig3), will take you to the page where you can write the script to create your workflow.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig3_HTML.png)

A screenshot of a banup-kubeforce window. Tab code is selected. Code is present on the left side of the window. The right side of the window contains feature actions under the tab marketplace. The featured actions are upload a build artifact and set up Java J D K.

Figure 3-3

Workflow page to create custom workflows

As you can see in Figure [3-3](#526597_1_En_3_Chapter.xhtml#Fig3), the workflow points to a `main.yaml` file that is created in the `./github/workflows` directory under your root project folder. You can also create the same file in your IDE and it will show up under the Actions tab once you commit the code. Listing [3-1](#526597_1_En_3_Chapter.xhtml#PC1) shows the sample code created for AWS Lambda.

```
name: CI
on:
push:
branches: [ "master" ]
jobs:
build-deploy:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v2
- uses: actions/setup-java@v2
with:
java-version: '8'
distribution: 'temurin'
cache: maven
- uses: aws-actions/setup-sam@v1
- uses: aws-actions/configure-aws-credentials@v1
with:
aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
aws-region: us-east-2
- name: Build with Maven
run: mvn -B package --file pom.xml
# sam package
- run: sam package --template-file template.yaml --output-template-file packaged.yaml --s3-bucket payrollbucket
# Run Unit tests- Specify unit tests here
# sam deploy
- run: sam deploy --no-confirm-changeset --no-fail-on-empty-changeset --stack-name payroll-aws --s3-bucket payrollbucket --capabilities CAPABILITY_IAM --region us-east-2
Listing 3-1
Workflow for Payroll Function to be Deployed on AWS Lambda
```

Let’s dive deep into the components of the YAML file.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig4_HTML.png)

A screenshot of workflow code elaboration. It contains name, on-push, jobs, runs-on ubuntu-latest, steps, uses, and run along with its details.

Figure 3-4

Work flow code elaboration

This sets up the GitHub Actions workflow and the triggers. Now, every time you commit or push code to this project repository in GitHub, this workflow will execute. Figure [3-5](#526597_1_En_3_Chapter.xhtml#Fig5) shows a sample execution of this workflow.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig5_HTML.png)

A screenshot of chapter 3 S C F plus Lambda. Build-deploy is selected under jobs. Build-deploy window contains set up job, run action, run a w s-actions, build with Maven, run Sam package and deploy, post run a w s, etc.

Figure 3-5

A successful execution of the workflow

## 3.2 ArgoCD

While GitHub Actions can push code to serverless environments such as Lambda, it lacks a good graphical representation of code deployed when it comes to the Kubernetes environment. Kubernetes is a orchestrator of containers and has a plethora services that manage deployments. ArgoCD was created for Kubernetes. ArgoCD is a declarative CD (Continuous Delivery) tool, which means application definitions, configurations, and environments can be version controlled. ArgoCD, similar to GitHub Actions, uses Git repositories as a single source of truth. This is also known as *GitOps*.

> *Declarative means configuration is guaranteed by a set of facts instead of by a set of instructions.*

Declarative GitOps allows the programmer or the ones who created the application to control the configuration of the environment in which the environment will run. This means the programmer does not have to rely on different teams, such as infrastructure or DevOps teams, to manage the pieces of the application. The programmers are in control, and this is a good thing.

ArgoCD set up is mostly programmatic and relies on the underlying Kubernetes configmaps. This is, as you can see, is different from other tools like Jenkins.

Here is how I set up the ArgoCD environment.

Prerequisites:

*   A Kubernetes cluster

*   Kubectl CLI configured to access your cluster

*   ArgoCD CLI installation instructions can be found at [`https://argo-cd.readthedocs.io/en/stable/cli_installation/`](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

Step 1: Create a namespace for ArgoCD.

Run the following command against your Kubernetes cluster:

```
$kubectl create namespace argocd
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig6_HTML.jpg)

A screenshot of creating an Argo C D namespace. The screenshot reads, dollar sign Kubectl create namespace Argo c d namespace forward slash Argo c d created.

Figure 3-6

Create an ArgoCD namespace

Step 2: install ArgoCD.

```
$kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This will go through a lengthy process to install ArgoCD. Once the installation is successful, you can validate it by using the following command:

```
$kubectl get all -n argocd
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig7_HTML.jpg)

A screenshot describes the status of an Argo C D installation run logs. It includes name, status, restarts, etc.

Figure 3-7

View the status of an ArgoCD installation

Now you can see that the ArgoCD services are up and running. Notice that an external IP has not been associated with `service/argocd-server`.

Run the following command to attach a LoadBalancer to the `argocd-server`:

```
$kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Now run the following command:

```
$kubectl get svc -n argocd
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig8_HTML.jpg)

A screenshot of the external I P of Argo c d-server run logs. It contains name, type, cluster-I P, external-I P, port, and age.

Figure 3-8

Take note of the external IP of argocd-server

You will see an external IP associated with `argocd-server`. This will allow you to connect to the `argocd-server`.

Before you start to use ArgoCD, you need to change the “admin” user password. You can use Kubectl to read the secret associated with the “admin” user.

Run the following command:

```
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig9_HTML.jpg)

A screenshot of getting the password of admin user. The screenshot depicts a code.

Figure 3-9

Get the password of the "admin" user

The output of this command is the password for the “admin” user.

You can now log in using the web browser. Then navigate to the 20.119.112.240 URL to change the password.

Log in to ArgoCD with the following command:

```
$argocd login 20.119.112.240
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig10_HTML.jpg)

A screenshot of logging in to Argo C D run logs. If it gets a warning error, the command is a certificate signed by an unknown authority. Run logs contain details and the admin logged in successfully.

Figure 3-10

Log in to ArgoCD

You have successfully installed and connected to ArgoCD. Figure [3-11](#526597_1_En_3_Chapter.xhtml#Fig11) shows a sample of the ArgoCD UI.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig11_HTML.png)

A screenshot of deploying an application in Argo C D U I. It depicts Payroll-h 2 which contains project, labels, status, repository, path, destination, etc. The status is healthy and synced.

Figure 3-11

ArgoCD UI showing an app deployed

Now that you have learned about GitHub Actions and ArgoCD, you can move on to deploying your application and automating the CI/CD process.

## 3.3 Building a Simple Example with Spring Cloud Function

You will use the same example from Chapter [2](#526597_1_En_2_Chapter.xhtml), but instead of using the `EmployeeConsumer` interface, this example uses `EmployeeSupplier`. In order to do that, you need a prepopulated database. You’ll then query the database using a supplier function. You can find the code at [`https://github.com/banup-kubeforce/payroll-h2`](https://github.com/banup-kubeforce/payroll-h2).

Here are the required changes.

Step 1: Create scripts to populate the H2 database when the function starts up. Create a schema that creates the `employee` table. Store the script in a `Schema.sql` file, as shown in Listing [3-2](#526597_1_En_3_Chapter.xhtml#PC9).

```
DROP TABLE IF EXISTS employee;
CREATE TABLE employee (
id INT AUTO_INCREMENT PRIMARY KEY,
name varchar(250),
employeeid varchar(250),
email varchar(250),
salary varchar(250)
);
Listing 3-2
Schema.sql
```

Populate the database with an `INSERT` statement. Then create a file called `Data.sql` and store the `INSERT` statement in it, as shown in Listing [3-3](#526597_1_En_3_Chapter.xhtml#PC10).

```
INSERT INTO employee (name, employeeid, email, salary) values
('banu','001','banup@yahoo.com','10000');
Listing 3-3
Data.sql
```

Add these two files to the resources folder of the main project, as shown in Figure [3-12](#526597_1_En_3_Chapter.xhtml#Fig12).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig12_HTML.jpg)

A screenshot of the spring boot project structure. In a project structure, the s r c folder is selected. It contains a resources folder. Resources folder includes data. s q l, and schema. s q l folders along with other folders.

Figure 3-12

Spring Boot project structure with data.sql and schema.sql

Modify the `Application.properties` as follows. Change `spring.cloud.function.definition` from `employeeConsumer` to `employeeSupplier`. This will route function calls to `employeeSupplier`. See Listing [3-4](#526597_1_En_3_Chapter.xhtml#PC11).

```
spring.cloud.function.definition=employeeSupplier
spring.datasource.url=jdbc:h2:mem:employee
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
Listing 3-4
Application.properties
```

Also add `spring.jpa.defer-datasource-initialization=true` to ensure that the data gets populated on startup.

No other code changes are required. It is important to note that the changes you made only affect the configuration file.

## 3.4 Setting Up a CI/CD Pipeline to Deploy to a Target Platform

As discussed in the introduction of this chapter, you’ll use two tools for the CI/CD process. GitHub Actions can be used as a tool for both CI (Continuous Integration) and CD (Continuous Deployment), while ArgoCD is a CD tool.

ArgoCD was designed for Kubernetes, so you can leverage this tool exclusively for Knative/Kubernetes deployment. You’ll use GitHub Actions for serverless environments such as Lambda.

Figure [3-13](#526597_1_En_3_Chapter.xhtml#Fig13) shows the flow when deploying to serverless environments like AWS Lambda, Google Cloud Functions, and Azure Functions.

The process steps are as follows:

1.  Create code and push/commit code to GitHub.

2.  GitHub Actions senses the event trigger of the commit and starts the build and deploy process to the serverless environments defined in the actions script.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig13_HTML.jpg)

A flow diagram of deploying to serverless functions. The code created is pushed to GitHub through Git flows repo name and branch name. Event-triggered from GitHub results in GitHub actions and deploys to spring cloud function.

Figure 3-13

Deploying to serverless functions environments

Figure [3-13](#526597_1_En_3_Chapter.xhtml#Fig13) shows the flow when deploying Spring Cloud Function to a Kubernetes environment with Knative configured.

The process steps are as follows:

1.  Create code and push/commit code to GitHub.

2.  GitHub Actions senses the event trigger of the commit and starts the build process and deploys the created container image into Docker Hub.

3.  ArgoCD polls for changes in GitHub and triggers a “sync.” It then retrieves the container image from Docker hub and deploys to Knative on Kubernetes. See Figure [3-14](#526597_1_En_3_Chapter.xhtml#Fig14).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig14_HTML.jpg)

A flow diagram describes deploying to a Knative-kubernetes environment. Commit code through Git is pushed to GitHub. Event-triggered GitHub actions are pushed to docker and synced and pulled to Argo. It deploys the spring cloud function on kubernetes.

Figure 3-14

Deploying to a Knative-Kubernetes environment

## 3.5 Deploying to the Target Platform

This section looks at the process of deploying the Spring Cloud Function to the target environments such as AWS Lambda, Google Cloud Functions, Azure Functions, and Knative on Kubernetes.

Here are the prerequisites for all the environments:

*   GitHub repository with code deployed to GitHub

*   Access and connection information to the environments

*   All Chapter [2](#526597_1_En_2_Chapter.xhtml) prerequisites for each of the environments. Refer to Chapter [2](#526597_1_En_2_Chapter.xhtml) for each environment

*   Use the successful deployments of Chapter [2](#526597_1_En_2_Chapter.xhtml) as a reference for each of the deploys

### 3.5.1 Deploying to AWS Lambda

Deploying to AWS Lambda requires using a SAM (Serverless Application Model) based GitHub Actions script. This section explains how to use SAM and GitHub Actions. There is no additional coding required.

Prerequisites:

*   AWS account

*   AWS Lambda Function subscription

*   S3 bucket to store the code build

*   AWS CLI (optional) to verify deployments through the CLI

*   Code from GitHub at [`https://github.com/banup-kubeforce/payroll-aws-h2`](https://github.com/banup-kubeforce/payroll-aws-h2)

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig15_HTML.jpg)

A flow diagram describes deploying spring cloud function with GitHub actions. Commit code through Git is pushed to GitHub. Event-triggered GitHub actions deploy spring cloud function on lambda.

Figure 3-15

Deploying Spring Cloud Function with GitHub Actions on AWS Lambda

Step 1: Spring Cloud Function code in GitHub.

Push the code to GitHub. You can bring down code from GitHub at [`https://github.com/banup-kubeforce/payroll-aws-h2`](https://github.com/banup-kubeforce/payroll-aws-h2). This code can be modified to your specs and deployed to the repository of your choice.

Step 2: Implement GitHub Actions with AWS SAM. AWS SAM (Serverless Application Model) is a framework for building serverless applications. More information can be found at [`https://aws.amazon.com/serverless/sam/`](https://aws.amazon.com/serverless/sam/).

AWS has a sample SAM-based actions script that is available in the GitHub marketplace that you can leverage. This script will execute the SAM commands.

The action code in Listing [3-5](#526597_1_En_3_Chapter.xhtml#PC12) can be created on the GitHub Actions dashboard or in your IDE

```
name: CI
on:
push:
branches: [ "master" ]
jobs:
build-deploy:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v2
- uses: actions/setup-java@v2
with:
java-version: '11'
distribution: 'temurin'
cache: maven
- uses: aws-actions/setup-sam@v1
- uses: aws-actions/configure-aws-credentials@v1
with:
aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
aws-region: us-east-2
- name: Build with Maven
run: mvn -B package --file pom.xml
# sam package
- run: sam package --template-file template.yaml --output-template-file packaged.yaml --s3-bucket payrollbucket
# Run Unit tests- Specify unit tests here
# sam deploy
- run: sam deploy --no-confirm-changeset --no-fail-on-empty-changeset --stack-name payroll-aws --s3-bucket payrollbucket --capabilities CAPABILITY_IAM --region us-east-2
Listing 3-5
Workflow for Payroll Function to be Deployed on AWS Lambda
```

The secrets for these two elements can be stored in GitHub secrets:

```
aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

Figure [3-16](#526597_1_En_3_Chapter.xhtml#Fig16) shows the place to store configuration secrets for GitHub Actions. It is under the Settings tab.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig16_HTML.png)

A screenshot of the ban up-Kubeforce window. The settings tab is selected. It displays action secrets and environmental secrets with its details.

Figure 3-16

Actions secrets for credentials and configuration

Step 3: Execute GitHub Actions. Once the workflow is configured, the actions can be triggered from the GitHub console or through a code commit, as defined by the following code in the `sam-pipeline.yaml` file.

```
on:
push:
branches: [ "master" ]
```

GitHub Actions execute the steps outlined in the YAML file and deploy the function to AWS Lambda. Figure [3-17](#526597_1_En_3_Chapter.xhtml#Fig17) shows a successful run.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig17_HTML.jpg)

A screenshot of the banup-kubeforce window. The build deploy button is selected. It displays a new window on build deploy which includes set up job, build with Maven, run Sam package, etc.

Figure 3-17

A successful execution of GitHub Actions on AWS Lambda

Step 4: Verify that the function is up and running in AWS Lambda.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig18_HTML.jpg)

A screenshot of A W S Lambda window. The function is selected. It displayed the function name.

Figure 3-18

Function created after execution of GitHub Actions

Since the `payroll-aws-h2` application exposes `EmployeeSupplier`, you will do a simple `GET` against the function to see if you get the result of data that has been inserted into the database on Spring Cloud Function startup.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig19_HTML.jpg)

A screenshot of the Lambda function window. It depicts function overview with its description.

Figure 3-19

Testing if the function was successful. See the JSON response

In Figure [3-19](#526597_1_En_3_Chapter.xhtml#Fig19), you can see that the test was successful and see a JSON result of what is in the database.

## 3.6 Deploying to GCP Cloud Functions

Deploying to GCP Cloud Functions using GitHub Actions is a bit intrusive, as you have to add a `MANIFEST.MF` file to the resources folder. See the code in GitHub.

Prerequisites:

*   A GitHub repository with the code. The code at [`https://github.com/banup-kubeforce/payroll-gcp-h2.git`](https://github.com/banup-kubeforce/payroll-gcp-h2.git) can be leveraged

*   Google `accountSubscribe` to Google Cloud Functions

*   Gcloud CLI is optional if you are just using the GitHub Actions dashboard

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig20_HTML.jpg)

A flow diagram describes G C P and GitHub actions flow. Commit code through Git is pushed to GitHub. Event-triggered GitHub actions deploy spring cloud function in google cloud function.

Figure 3-20

GCP and GitHub Actions flow

Step 1: Spring Cloud Function code in GitHub. Push your code to GitHub. If you have cloned the [`https://github.com/banup-kubeforce/payroll-gcp-h2.git`](https://github.com/banup-kubeforce/payroll-gcp-h2.git) then you have everything that you need to push the code to your repository.

Step 2: Set up Cloud Functions actions.

Set up GitHub actions to run the Cloud Functions command. You have two choices:

*   Use `deploy-cloud-functions runner`

*   Use `gcloud-cli`

Listing [3-6](#526597_1_En_3_Chapter.xhtml#PC15) shows the GitHub Actions file.

```
name: Google Cloud Functions
on:
push:
branches: [ "master" ]
jobs:
build-deploy:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v2
- uses: actions/setup-java@v2
with:
java-version: '11'
distribution: 'temurin'
cache: maven
- name: Build with Maven
run: mvn -B package --file pom.xml
- id: 'auth'
uses: 'google-github-actions/auth@v0'
with:
credentials_json: '${{ secrets.GCP_CREDENTIALS }}'
- name: 'Set up Cloud SDK'
uses: 'google-github-actions/setup-gcloud@v0'
- name: 'Use gcloud CLI'
run: 'gcloud functions deploy payroll-gcp --entry-point org.springframework.cloud.function.adapter.gcp.GcfJarLauncher --runtime java11 --trigger-http --source target/deploy --memory 512MB'
Listing 3-6
Workflow for Payroll Function to be Deployed on GCP Cloud Functions
```

Note that you will have to store your `GCP_CREDENTIALS` in the GitHub Secrets dashboard.

As in the previous example with AWS Lambda, note that the steps to check out, set up, and build Maven are the same. For the authentication and deployment, you use the Google Cloud CLI. The Set up Cloud SDK task will download and set up the Google CLI. You can use the same command line script that you used when you deployed from a laptop in Chapter [2](#526597_1_En_2_Chapter.xhtml).

Step 3: Commit and push code to trigger the GitHub Actions. This trigger is defined in the actions code. In this example, any push or commit to the “master” branch will trigger the GitHub Actions.

```
on:
push:
branches: [ "master" ]
```

This can be done on the GitHub Actions website or in the IDE. You can go to GitHub and commit a change by doing a simple modification at the “master” branch. This will start the GitHub Action flow.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig21_HTML.jpg)

A screenshot of the banup-kubeforce window. The build deploy button is selected. A new window on build deployment is displayed, it includes set up job, build with Maven, run Sam package, etc.

Figure 3-21

GitHub Actions gets triggered by a code commit

Once the actions successfully complete the job, you can go to the Google Cloud Functions dashboard and test the function. Again, you execute a simple GET against the `EmployeeSupplier` function.

Step 4: Test the function.

Before you test the function, ensure that you pick the function to be invoked from an unauthenticated device such as your laptop. Once you’re done testing, remove the privilege to avoid unnecessary invocations.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig22_HTML.png)

A screenshot of the unauthenticated set for payroll-g c p- h 2 which is allowed. It includes a link to 1st gen payroll along with other details.

Figure 3-22

Allow unauthenticated set for Payroll-gcp-h2

You can go to the console of your function in the Google Cloud Functions dashboard and execute the test. You do not have to provide any input; simply click the Test the Function button to execute the test. You will see the output of `EmployeeSupplier` in the Output section, as shown in Figure [3-23](#526597_1_En_3_Chapter.xhtml#Fig23).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig23_HTML.jpg)

A screenshot of the google cloud window. It displays cloud functions. In payroll-g c p-h 2, a function is selected. It configures triggering events and displays the test command.

Figure 3-23

Successful output of the Spring Cloud Function test in GCP Cloud Functions

## 3.7 Deploying to Azure Functions

Spring Cloud Function on Azure Functions require a bit of tweaking, as you learned in Chapter [2](#526597_1_En_2_Chapter.xhtml). This is because the configuration is not externalized, as with AWS Lambda or GCP Cloud Functions. This does not mean that you cannot deploy easily. You have to understand how Azure Function code interprets Spring Cloud Function code and execute. See Chapter [2](#526597_1_En_2_Chapter.xhtml) for discussions around this issue; make sure that you execute and test locally before pushing to the Azure cloud.

Step 1: Spring Cloud Function code in GitHub. Push your code to GitHub. If you cloned the [`https://github.com/banup-kubeforce/payroll-azure-h2.git`](https://github.com/banup-kubeforce/payroll-azure-h2.git), you have everything that you need to push the code to your repository.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig24_HTML.jpg)

A flow diagram describes flow of spring cloud function deployment on azure. Commit code through Git is pushed to GitHub. Event-triggered GitHub actions and deploy spring cloud function in Azure functions.

Figure 3-24

Flow of Spring Cloud Function deployment on Azure

Step 2: Set up Azure Function App Actions. See Listing [3-7](#526597_1_En_3_Chapter.xhtml#PC17).

```
name: Deploy Java project to Azure Function App
on:
push:
branches: [ "master" ]
# CONFIGURATION
# For help, go to https://github.com/Azure/Actions
#
# 1\. Set up the following secrets in your repository:
#   AZURE_FUNCTIONAPP_PUBLISH_PROFILE
#
# 2\. Change these variables for your configuration:
env:
AZURE_FUNCTIONAPP_NAME: payroll-kubeforce-new      # set this to your function app name on Azure
POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
POM_FUNCTIONAPP_NAME: payroll-kubeforce-new       # set this to the function app name in your local development environment
JAVA_VERSION: '11'                      # set this to the java version to use
jobs:
build-and-deploy:
runs-on: ubuntu-latest
environment: dev
steps:
- name: 'Checkout GitHub Action'
uses: actions/checkout@master
- name: Setup Java Sdk ${{ env.JAVA_VERSION }}
uses: actions/setup-java@v1
with:
java-version: ${{ env.JAVA_VERSION }}
- name: 'Restore Project Dependencies Using Mvn'
shell: bash
run: |
pushd './${{ env.POM_XML_DIRECTORY }}'
mvn clean package
popd
- name: 'Run Azure Functions Action'
uses: Azure/functions-action@v1
id: fa
with:
app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
# package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
#        package: '${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
#  package: 'target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
Listing 3-7
Workflow for Payroll Function to be Deployed on Azure Functions
```

Step 3: Execute GitHub Actions. GitHub Actions are executed by this setting in the `actions` file:

```
on:
push:
branches: [ “master” ]
```

Any commit or push to the “master” branch will trigger an execution of GitHub Actions; see Figure [3-25](#526597_1_En_3_Chapter.xhtml#Fig25).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig25_HTML.jpg)

A screenshot of the summary window. The build and deploy button is selected which displays build deployment window which includes set up job, build with Maven, run Sam package, etc.

Figure 3-25

Successful deployment of Payroll Spring Cloud Function using GitHub Actions

After successfully deploying using GitHub actions, you need to verify the deployment in the Azure Functions dashboard; see Figure [3-26](#526597_1_En_3_Chapter.xhtml#Fig26).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig26_HTML.png)

A screenshot of the azure functions dashboard. Functions selected under payroll-kubeforce includes name, trigger and status with its details.

Figure 3-26

Azure Functions dashboard showing the function employeeSupplier has been deployed

Click the `employeeSupplier` link to get to Figure [3-27](#526597_1_En_3_Chapter.xhtml#Fig27).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig28_HTML.jpg)

A screenshot of the employee supplier dashboard. The tab overview is selected. It displays the Get function U r l along with the default function key and ok tab.

Figure 3-28

Get the URL of the function

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig27_HTML.jpg)

A screenshot of the employee supplier dashboard. The tab overview is selected. The get function under overview is selected which displays essentials along with other details such as function app, status, etc.

Figure 3-27

Click the Get Function URL on the employeeSupplier dashboard

The URL of the function is [`https://payroll-kubeforce.azurewebsites.net/api/employeeSupplier`](https://payroll-kubeforce.azurewebsites.net/api/employeeSupplier). Use this URL for testing.

Step 4: Testing. You will use an external testing tool to see if the deployed functions work. The tool you use here is Postman.

You can simply use a `GET` operation to test, as shown in Figure [3-29](#526597_1_En_3_Chapter.xhtml#Fig29).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig29_HTML.jpg)

A screenshot of the window of testing of the payroll-Kubeforce azure website. The tab body is selected. It displays options raw, binary, and Graph Q L along with other details.

Figure 3-29

Successful test result with Postman

This completes the deployment of Spring Cloud Function on Azure Functions using GitHub Actions.

## 3.8 Deploying to Knative on Kubernetes

The CI/CD for deploying Spring Cloud Function on Knative are similar for every Kubernetes. The only change is the cluster name. This section uses ArgoCD ([`http://argoproj.github.io`](http://argoproj.github.io)) for CD even though you can achieve the same result with GitHub Actions. I found GitHub Actions a bit code-intensive. I wanted to separate the CD process and have a good visual tool that shows the deployment. ArgoCD provides a good visual interface.

To have a common repository for all the cloud environments, you’ll use Docker hub in this example. Docker hub provides a good interface for managing images and it is popular with developers. If you use ECR, GCR, or ACR, you’ll experience vendor lock-in.

The prerequisites for deploying to any Kubernetes platform are the same:

*   Get the code from GitHub. You can use [`https://github.com/banup-kubeforce/payroll-h2.git`](https://github.com/banup-kubeforce/payroll-h2.git) or push your custom code

*   A Dockerhub account

*   A Dockerfile to push to Dockerhub

*   Actions code in your GitHub project

*   Access to a Kubernetes Cluster with Knative configured

*   ArgoCD up and running

*   An app in ArgoCD that is configured to poll the GitHub project

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig30_HTML.jpg)

A flow diagram describes deployment flow for spring cloud function with Github actions and Argo C D. Commit code through Git pushes it to GitHub. Event-triggered Github actions push to docker and pull and sync to Argo. It deploys spring cloud function in kubernetes.

Figure 3-30

Deployment flow for Spring Cloud Function with GitHub Actions and ArgoCD

Once you have the prerequisites set up, you can begin configuring an automated CI/CD pipeline. For this example implementation, you’ll use the code from GitHub at [`https://github.com/banup-kubeforce/payroll-h2.git`](https://github.com/banup-kubeforce/payroll-h2.git).

Step 1: Spring Cloud Function code in GitHub. Push your code to GitHub. You can use the code for `payroll-h2` in GitHub.

Step 2: Create a GitHub Action. Listing [3-8](#526597_1_En_3_Chapter.xhtml#PC19) shows the code for the action.

```
name: ci
on:
push:
branches:
- 'main'
jobs:
docker:
runs-on: ubuntu-latest
steps:
-
name: Set up QEMU
uses: docker/setup-qemu-action@v2
-
name: Set up Docker Buildx
uses: docker/setup-buildx-action@v2
- uses: actions/checkout@v2
- uses: actions/setup-java@v2
with:
java-version: '8'
distribution: 'temurin'
cache: maven
-
name: Login to DockerHub
uses: docker/login-action@f054a8b539a109f9f41c372932f1ae047eff08c9
with:
username: ${{ secrets.DOCKERHUB_USERNAME }}
password: ${{ secrets.DOCKERHUB_TOKEN }}
- name: Extract metadata (tags, labels) for Docker1
id: meta
uses: docker/metadata-action@98669ae865ea3cffbcbaa878cf57c20bbf1c6c38
with:
images: banupkubeforce/springcloudfunctions
- name: Build with Maven
run: mvn -B package --file pom.xml
- name: Build and push Docker image
uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
with:
context: .
file: ./DockerFile
push: true
tags: ${{ steps.meta.outputs.tags }}
labels: ${{ steps.meta.outputs.labels }}
Listing 3-8
Workflow for Payroll Function Image to be Pushed to Docker Hub
```

This code creates a Docker image and pushes it to the Docker hub. You can store the username and password as secrets in the GitHub site (see Figure [3-31](#526597_1_En_3_Chapter.xhtml#Fig31)).

```
username: ${{ secrets.DOCKERHUB_USERNAME }}
password: ${{ secrets.DOCKERHUB_TOKEN }}
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig31_HTML.png)

A screenshot of the payroll-Kubeforce window. In settings, action is selected. It displays environment and repository secrets and their details.

Figure 3-31

GitHub Secrets store for configurations and credentials

Step 3: Execute GitHub Actions to build and push the Docker image.

The execution of GitHub Actions can be triggered by a push/commit. The trigger is defined in the GitHub Actions YAML file:

```
on:
push:
branches:
- 'main'
```

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig32_HTML.jpg)

A screenshot of the banup-kubeforce window. The docker button is selected. under docker the following is displayed setup job, set up Q E M U, build with Maven, run the Sam package, etc.

Figure 3-32

Successful run of GitHub Actions

Step 4: Configure ArgoCD.

Follow the steps outlined in the introduction of this chapter for ArgoCD. You need to connect to your cluster on your local machine before executing this command; see Listing [3-9](#526597_1_En_3_Chapter.xhtml#PC22).

```
$ argocd app create payroll-h2 --repo https://github.com/banup-kubeforce/payroll-h2.git --path knative --dest-server https://kubernetes.default.svc --dest-namespace default –upsert
Listing 3-9
ArgoCD Script to Create a Project and Point to the payroll-h2 Repo
```

This will create `app payroll-h2` in ArgoCD, as shown in Figure [3-33](#526597_1_En_3_Chapter.xhtml#Fig33).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig33_HTML.jpg)

A screenshot of the application window. It depicts the Payroll-h 2 which includes project, labels, status, repository, path, destination, etc. The status is healthy and synced.

Figure 3-33

Application payroll-h2 deployed on ArgoCD

Step 5: Sync the project in Argo CD.

Now that you have created the app and it is pointing to the GitHub repository, make sure you have a connection to the repo, as shown in Figure [3-34](#526597_1_En_3_Chapter.xhtml#Fig34). I connected to the repo using HTTPS. This will allow the app to poll for changes and trigger the flow to push the Docker image to the specified Kubernetes environment.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig34_HTML.jpg)

A screenshot of the repository window. It depicts type, name, repository, and connection status.

Figure 3-34

Connect payroll-h2 to the repo using HTTPS

You can also run a deployment manually by clicking SYNC, as shown in Figure [3-35](#526597_1_En_3_Chapter.xhtml#Fig35).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig35_HTML.jpg)

A screenshot of payroll-h 2 window. It depicts the project, labels, status, repository, target, path, destination, and name. The status is healthy and synced.

Figure 3-35

The SYNC button on payroll-h2 app in ArgoCD

Figure [3-36](#526597_1_En_3_Chapter.xhtml#Fig36) shows a successful sync process in ArgoCD.

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig36_HTML.png)

A screenshot of deploying the application window. Select synced button. Payroll-h 2 is displayed. It includes app health, current synchronize status, and last synchronize result.

Figure 3-36

Successful run of the sync showing the deployment flow

Step 6: Check if the function has been deployed. Navigate to the Kubernetes dashboard on the Azure Portal and verify that the service has been deployed. See Figure [3-37](#526597_1_En_3_Chapter.xhtml#Fig37).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig37_HTML.jpg)

A screenshot of Kubernetes services dashboard. It displays payroll c l s t r of services and ingresses. Services and ingresses are selected and the status all are ok.

Figure 3-37

Successful deployment of Spring Cloud Function (payroll-h2) on Azure

Step 7: Testing. The best way to get the URL to test is to connect to the cluster via the command line and get the URL, as explained in Chapter [2](#526597_1_En_2_Chapter.xhtml).

Run `$kn service list` to get the URL for testing, as shown in Figure [3-38](#526597_1_En_3_Chapter.xhtml#Fig38).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig38_HTML.jpg)

A screenshot of service list run logs. It contains name, U R L, latest, age, conditions, ready, and reason.

Figure 3-38

URL for testing payroll-h2

You can use Postman for testing. See Figure [3-39](#526597_1_En_3_Chapter.xhtml#Fig39).

![](images/526597_1_En_3_Chapter/526597_1_En_3_Fig39_HTML.jpg)

A screenshot of the Get window. params selected. It contains query parameters of key, value, description, and bulk edit. The body includes a code with other tabs such as pretty, raw, preview, etc.

Figure 3-39

Successful execution of test against payroll-h2 deployed on AKS Knative

This completes the successful deployment using GitHub Actions and ArgoCD.

## 3.9 Summary

In this chapter, you learned how to set up some CI/CD tools to create an automated deployment for your Spring Cloud Function.

You learned how to trigger the deployment of functions on Lambda, Google Cloud Functions, and Azure Functions.

You also learned that you can combine the build of Docker images stored in Docker hub and ArgoCD to deploy the image to any Kubernetes cluster that is running Knative.

If you want to achieve “write-once deploy-anywhere,” you have to look at using Kubernetes and Knative. Spring Cloud Function is really a portable function.

# 4. Building Event-Driven Data Pipelines with Spring Cloud Function

Spring Cloud Function plays a critical role in the hybrid cloud or on-premises/private cloud space. Building events/data pipelines that span across the local datacenter and the cloud increases complexity due to the firewall boundaries. Data pipelines play an important role when you want to acquire, move, or transform data as it comes (streaming) or when it’s offline (batch).

So, what are event data pipelines?

## 4.1 Data Event Pipelines

Let’s look at an example. Say you are driving a car and suddenly you collide with a vehicle. After a few minutes, you get a call from OnStar asking you if you are alright as you were in a collision. How did OnStar know that you were in a collision? All this happens in a flash. Sensors in your vehicle are triggered by the collision event and begin sending data as streams into the OnStar system. The OnStar system processes the data from numerous other systems and triggers a response in the form of an OnStar operator calling you. The event that triggered the data processing along with the output is the event data pipeline. Figure [4-1](#526597_1_En_4_Chapter.xhtml#Fig1) illustrates this process.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig1_HTML.jpg)

An illustration depicts the OnStar event-driven data pipeline implementation for vehicles. The vehicle sends data to nearby Cell tower, Cell tower relays data to OnStar datacenter. OnStar datacenter routes information to the call center. Through this emergency services such as police, medical response team and towing company are activated.

Figure 4-1

An example event-driven data pipeline implementation for vehicles using OnStar

All data regarding the incident is collated, processed, analyzed, and sent to various target systems, both internal and external to OnStar. This is an example of event data pipeline.

A data pipeline is a set of processes that takes raw data from disparate sources, and then filters, transforms, and moves the data to target stores, where it can be analyzed and presented in a visually compelling way.

The data can be in the form of streams or batches. Streaming is usually associated with real-time to near real-time data movement and batches are usually scheduled and non-real-time. Figure [4-2](#526597_1_En_4_Chapter.xhtml#Fig2) illustrates this.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig2_HTML.png)

An illustration depicts the data pipeline as a set of processes that ingests the structured or unstructured data and retrieves and transforms the data through data lakes or data warehouses which is further analyzed through A I or M L processes.

Figure 4-2

Data pipeline process

The data can also be unstructured or structured. Unstructured data can be from a multitude of resources, including Internet-based platforms such as Twitter, to an automotive device emanating data.

Structured data usually originates from within the company’s applications. This data needs to be combined at a landing zone of object stores. These object stores can be on-premises or in the cloud. Once the data is ingested into the object stores, it is then retrieved and transformed, usually through an ETL process in other datastores such as data lakes or data warehouses. This data is then further analyzed using BI tools such as Power BI and Tableau or further processed with the AI/ML processes.

This whole process—from data ingestion to consumption—is called the data pipeline.

Let’s dive a bit deeper into the various sub-processes outlined in the data pipeline process.

### 4.1.1 Acquire Data

Acquiring data is the first step in the data pipeline process. Here, business owners and data architects decide on what data to use to fulfil the requirements for a specific use case.

For example, in the case of a collision event detection for OnStar, the data needs to be acquired from sensors. This sensor data then needs to be combined with data from internal and external partners, like finance, towing partners, rental partners, and so on.

Table [4-1](#526597_1_En_4_Chapter.xhtml#Tab1) shows the data, the type of data, and the sources for a vehicle event-driven pipeline.

Table 4-1

Data Identification

| Data | Type of Data | Data Source |
| --- | --- | --- |
| **Sensor** | Unstructured (JSON, CSV) | Sensors |
| **Rental info** | Relational (RDBMS) | External rental agency |
| **User info** | Relational (RDBMS) | OnStar registration |
| **Vehicle info** | Relational (RDBMS) | Vehicle data systems |
| **Towing info** | Relational (RDBMS) | External towing company |

### 4.1.2 Store/Ingest Data

The data from various sources is acquired and stored as raw data into the store of choice. The method of ingestion can be stream-based or batch-based.

For example, in the case of OnStar, sensor data is streamed at regular intervals or is event-based. Other data, such as rental info, can either be batch based or on-demand query driven. The raw datastore can be an S3 object store hosted in the cloud or on-premises.

### 4.1.3 Transform Data

The data that is stored raw in the object store is then processed. The process may include converting or transforming unstructured data into structured data and storing it in an RDBMS database.

For example, in OnStar, partner data and internal data will be combined and transformed into a common data model. The sensor data will also be transformed into an RDBMS format.

### 4.1.4 Load Data

Once the data is transformed, it is then loaded into a single or multiple databases with a common schema. This schema is based on a predefined data model specific to the use case. The target datastore can be a data lake or another RDBMS store. Here again, it depends on the type of analysis that needs to be done.

For example, if this an OLTP type of analysis, the data needs to be processed and sent to requesting systems quickly. This would require an RDBMS store. Data that needs to be available for reporting and research can be stored in a data lake.

### 4.1.5 Analyze Data

During this sub-process, the data that is stored in a data lake or RDBMs will be analyzed using tools such as Tableau, Power BI, or a dedicated web page for reporting.

In the case of OnStar, data that is stored in the data lake will be analyzed using Tableau or Power BI, while the data that needs immediate attention will be analyzed by a custom dashboard or reporting interface on the web.

Spring Cloud Function plays an integral role in the whole process, especially when combined with tools such as Spring Cloud Data Flow, AWS Glue, Azure Data Factory, Google’s data flow, and so on. You will dive deep into these tools in this chapter.

## 4.2 Spring Cloud Function and Spring Cloud Data Flow and Spring Cloud Streams

Spring Cloud Data Flow (SCDF) is a Spring.io-based product that supports the creation and deployment of a data pipeline. It supports batch and event-driven data, which makes it versatile. SCDF pipelines can be built programmatically or wired up through a GUI. It is heavily based on the Spring Framework and Java, which makes it very popular among Spring developers.

Figure [4-3](#526597_1_En_4_Chapter.xhtml#Fig3) shows a sample dashboard of SCDF with a simple data pipeline that has a source, a processor, and a sink. You can drag-and-drop from the available components to build the pipeline. You can also build your custom source, processors, and sinks and deploy them for use within your enterprise.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig3_HTML.jpg)

A screenshot of a S C D F. It shows the stream vehicle data pipelines definition, status and applications.

Figure 4-3

A sample graphical representation of a data pipeline in SCDF

As you can see from the dashboard, you can build stream-based or batch-based (task) data pipelines and manage these through a single dashboard.

SCDF, unlike other the data pipeline tools available in the cloud, can be deployed in a Kubernetes, Docker, or Cloud Foundry environment, making it a portable tool for data pipeline development and deployment.

### 4.2.1 Spring Cloud Function and SCDF

Spring Cloud Function and SCDF are perfectly matched, as they are built out of the same framework, Spring. You can deploy Spring Cloud Function as a source, processor, sink, or as a trigger for the pipeline. Since the data pipelines are usually invoked sporadically for processing data, you can optimize utilization of resources and costs with a Spring Cloud Function.

Let’s look at a sample implementation of Spring Cloud Function with SCDF.

In this example, you will build a simple data pipeline using RabbitMQ as a source, do a simple transformation, and store the messages in a log. You will publish sample vehicle information into a RabbitMQ topic called `VehicleInfo` and do a simple transformation, then store it in a log file.

RabbitMQ ➤ Transform ➤ Log

Prerequisites:

*   SCDF deployed on Kubernetes or locally in Docker

*   Kubernetes or Docker

*   A RabbitMQ cluster/instance

*   A queue to publish messages

*   Code from GitHub at [`https://github.com/banup-kubeforce/SCDF-Rabbit-Function.git`](https://github.com/banup-kubeforce/SCDF-Rabbit-Function.git)

Additional prerequisites for each environment can be found at [`https://dataflow.spring.io/docs/installation`](https://dataflow.spring.io/docs/installation)

Step 1: Installing Spring Cloud Data Flow.

*   Local machine: Docker Compose is an easy way to get an instance of Spring Cloud Data Flow up and running. The details of the installation are available at [`https://dataflow.spring.io/docs/installation/local/docker/`](https://dataflow.spring.io/docs/installation/local/docker/)

*   Kubernetes: Instructions for installing this on Kubernetes are available at[`https://dataflow.spring.io/docs/installation/kubernetes/`](https://dataflow.spring.io/docs/installation/kubernetes/).

For this example, I used an existing cluster that I created in Chapter [2](#526597_1_En_2_Chapter.xhtml) and ran the following `helm` command to install the SCDF, which results in the output shown in Figure [4-4](#526597_1_En_4_Chapter.xhtml#Fig4).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig4_HTML.jpg)

A screenshot depicts the helm command to install the S C D F. It consists of name, last deployed, namespace, status, test suite, notes, chart name, chart version and app version.

Figure 4-4

Successful execution of a helm chart for SCDF

```
$helm install scdf bitnami/spring-cloud-dataflow -n scdf-system --set server.service.type=LoadBalancer --set server.service.loadBalancerIP=${ingress} --set server.ingress.enabled=true --set server.ingress.server.host=scdf.${ingress}.xip.io
```

Once the command runs successfully, as shown in Figure [4-4](#526597_1_En_4_Chapter.xhtml#Fig4), you can check the status by running `kubectl get pods`. You can also get the external IP with the `kubectl get svc` commands, as shown in Figures [4-5](#526597_1_En_4_Chapter.xhtml#Fig5) and [4-6](#526597_1_En_4_Chapter.xhtml#Fig6).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig6_HTML.jpg)

A screenshot depicts the command to get the status of the S C D F. The command used is dollar kubect 1 get s v c-n s c d f-system.

Figure 4-6

Get the status of SCDF

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig5_HTML.jpg)

A screenshot depicts the command to get external I P of the S C D F. The command used is dollar kubect 1 get pods-n s c d f-system.

Figure 4-5

Get the external IP of the SCDF

```
$kubectl get pods -n scdf-system
```

```
$kubectl get svc -n scdf-system
```

You can now access SCDF using the external IP. For example, [`http://20.241.228.184:8080/dashboard`](http://20.241.228.184:8080/dashboard)

The next step is to add applications. Spring provides a standard set of templates that you can use to build your pipeline.

Step 2: Add Applications to your SCDF Instance

Use the Add Application(s) button to create some starter apps that you can use to create a data pipeline; see Figures [4-7](#526597_1_En_4_Chapter.xhtml#Fig7) and [4-8](#526597_1_En_4_Chapter.xhtml#Fig8).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig8_HTML.jpg)

A screenshot depicts the options to add applications and docker-based or rabbit M Q kafka-based.

Figure 4-8

Pick the starters of your choice. If SDCF is deployed in Kubernetes, pick Docker based starters

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig7_HTML.jpg)

A screenshot depicts the S C D F add application view to create a data pipeline.

Figure 4-7

Add application view in SCDF

Figure [4-8](#526597_1_En_4_Chapter.xhtml#Fig8) shows you the options to add applications that are custom built through the Registering One or More Applications option. You import the application coordinates from an HTTP URI or use a properties file. There is also an option to import some prebuilt starters from Spring. Furthermore, you can choose starters that are Maven- or Docker-based or RabbitMQ- or Kafka-based. RabbitMQ and Kafka are used as backbone messaging systems for internal SCDF components and not for external use. When deploying to a Kubernetes cluster, you have to choose a Docker-based starter. When deploying locally in a Docker environment, you can choose between Maven and Docker-based starters.

Figure [4-9](#526597_1_En_4_Chapter.xhtml#Fig9) shows prebuilt templates with the Docker images URI. SCDF is running on a Kubernetes environment, so the prebuilt images have a Docker URI.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig9_HTML.jpg)

A screenshot depicts the S C D F running on a Kubernetes environment and prebuilt templates installed in S C D F.

Figure 4-9

Prebuilt templates are installed in SCDF

These prebuilt templates come in three categories—source, processor, and sink. They allow you to wire up a data pipeline without the need for coding. If you want a custom component, you can follow the examples in [`https://dataflow.spring.io/docs/stream-developer-guides/`](https://dataflow.spring.io/docs/stream-developer-guides/).

The next step is to create a stream using the starter templates you loaded.

Step 3: Create a Stream

The Streams dashboard allows you to wire up a data pipeline. Click the Create Streams button to start the process, as shown in Figure [4-10](#526597_1_En_4_Chapter.xhtml#Fig10).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig10_HTML.jpg)

A screenshot depicts the streams button to create a stream in the streams dashboard.

Figure 4-10

The Streams dashboard

Then perform the following steps:

1.  **Pick the source.**

This example uses a RabbitMQ as the source, so pick Rabbit from the available options, as shown in Figure [4-11](#526597_1_En_4_Chapter.xhtml#Fig11).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig11_HTML.jpg)

A screenshot depicts the create stream and picking the available option from the source.

Figure 4-11

Create a source

1.  **Pick a processor.**

Pick the Transform component from the list, as shown in Figure [4-12](#526597_1_En_4_Chapter.xhtml#Fig12).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig12_HTML.jpg)

A screenshot depicts a selection and creation of a processor for the transform component.

Figure 4-12

Create a processor

1.  **Pick a sink.**

Pick the Log component from the list, as shown in Figure [4-13](#526597_1_En_4_Chapter.xhtml#Fig13).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig13_HTML.png)

A screenshot depicts a selection and creation of a sink for the log component.

Figure 4-13

Create a sink

Now you wire them by dragging from the output of the first component to the input of the second component, as depicted in Figure [4-14](#526597_1_En_4_Chapter.xhtml#Fig14).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig14_HTML.png)

A screenshot depicts wiring the data pipeline by dragging the output of the first component to the input of the second component.

Figure 4-14

Wiring the data pipeline

The next step is to configure the pipeline.

1.  **Configure RabbitMQ.**

You have to point the Rabbit source to your RabbitMQ instance. There are three categories in the form to populate. Open and fill in the appropriate fields. I used username, password, port, host, and queue, as shown in Figure [4-15](#526597_1_En_4_Chapter.xhtml#Fig15).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig15_HTML.jpg)

Two screenshot depicts the configured Rabbit M Q and the properties of the Rabbit. It consists of a username, password, port and host.

Figure 4-15

Set the properties for rabbit

Click Update once you’re done with the configuration.

1.  **Configure the transform.**

In this example, you simply transform the incoming string to uppercase. Note that you have to use SpEL (Spring Expression Language). More information on SpEL Is available at [`https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.xhtml`](https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.xhtml). See Figure [4-16](#526597_1_En_4_Chapter.xhtml#Fig16).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig16_HTML.jpg)

A screenshot depicts the properties for transform. It consists of general and spel. function and update and cancel buttons.

Figure 4-16

Set the transform properties

1.  **Configure the sink.**

In this case, you just use a simple log. No configuration is required. See Figure [4-17](#526597_1_En_4_Chapter.xhtml#Fig17).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig17_HTML.jpg)

A screenshot depicts setting properties for log. It represents general, log along with cancel and update buttons.

Figure 4-17

Setting properties for the log

1.  **Wire up the data pipeline.**

Once you wire up, you will get a data pipeline. This will also show a Stream DSL (Domain Specific Language) expression that you can save and reuse using a SCDF shell or dashboard, as shown in Figure [4-18](#526597_1_En_4_Chapter.xhtml#Fig18).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig18_HTML.png)

A screenshot depicts the wiring up of the data pipeline. It represents stream D S L expression on create stream dashboard.

Figure 4-18

Wire up the data pipeline

```
rabbit --password=pa55word --port=5672 --host=20.40.208.246 --username=banup --queues=VehicleInfo | transform --expression='new String(payload).toUpperCase()' |log
```

The pipeline can be triggered by an external Spring Cloud Function on Lambda or using Google Cloud Functions, and so on. See Figure [4-19](#526597_1_En_4_Chapter.xhtml#Fig19).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig19_HTML.png)

A screenshot depicts the successful deployment of a data pipeline by an external Spring Cloud Function.

Figure 4-19

Successful deployment of a data pipeline

1.  **Deploy the stream**

Step 4: Create a function to publish data to RabbitMQ

Here, you create a Spring Cloud Function to publish the data to start the data-pipeline process.

The components that you will create are as follows:

*   `SenderConfig`

*   `QueueSender`

*   `SenderFunction`

Prerequisites:

*   A POM with the necessary dependencies for RabbitMQ and Spring Cloud Functions, as shown in Listing [4-1](#526597_1_En_4_Chapter.xhtml#PC5).

```

org.springframework.boot
spring-boot-starter-data-jpa

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-starter-amqp

org.springframework.cloud
spring-cloud-function-web

org.springframework.boot
spring-boot-starter-test
test

org.springframework.cloud
spring-cloud-dependencies
${spring-cloud.version}
pom
import

Listing 4-1
pom.xml with RabbitMQ Dependencies
```

Add the RabbitMQ server and configuration references to the `application.properties` file, as in Listing [4-2](#526597_1_En_4_Chapter.xhtml#PC6).

```
spring.cloud.function.definition=senderFunction
spring.rabbitmq.host=20.40.208.246
spring.rabbitmq.port=5672
spring.rabbitmq.username=banup
spring.rabbitmq.password=pa55word
queue.name=VehicleInfo
Listing 4-2
application.properties
```

     1.     **Create the SenderConfig Component**

This is a simple setter for the queue name. You can expand this to include other RabbitMQ configurations. This is the entity definition (see Listing [4-3](#526597_1_En_4_Chapter.xhtml#PC7)).

```
package com.kubeforce.scdffunctiontigger;
import org.springframework.amqp.core.Queue;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class SenderConfig {
@Value("${queue.name}")
private String message;
@Bean
public Queue queue() {
return new Queue(message, true);
}
}
Listing 4-3
application.properties
```

     2.     **Create the QueueSender Component**

You use a `RabbitTemplate` from the `springframework amqp` library to define the connection to the RabbitMQ instance (see Listing [4-4](#526597_1_En_4_Chapter.xhtml#PC8)).

```
package com.kubeforce.scdftrigger;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component
public class QueueSender {
@Autowired
private RabbitTemplate rabbitTemplate;
@Autowired
private Queue queue;
public void send(String order) {
rabbitTemplate.convertAndSend(this.queue.getName(), order);
}
}
Listing 4-4
QueueSender.java
```

     3.     **Wrap the Sender in Spring Cloud Function framework**

This example uses the `queueSender` to send the data (see Listing [4-5](#526597_1_En_4_Chapter.xhtml#PC9)).

```
package com.kubeforce.scdftrigger;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.function.Function;
public class SenderFunction implements Function {
@Autowired
private QueueSender queueSender;
@Override
public String apply(String s) {
queueSender.send("Vehicle:SUV,Make:Ford,Model:Edge,Year:2021");
return "ok. done";
}
}
Listing 4-5
SenderFunction.java
```

Step 5: Test the function using Postman

Use the `GET` function on Postman and provide the URL to the `senderFunction`.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Figa_HTML.jpg)

A screenshot depicts the G E T function on Postman with the U R L.

You should get the result shown in this image.

Check the RabbitMQ queue for any messages.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Figb_HTML.jpg)

A screenshot depicts the Rabbit M Q Queue for any messages. It represents Exchange, Routing key, Redelivered, Properties, and Payload under the Queries tab with a message the server reported 0 messages remaining.

Now you have a function that can post messages to a RabbitMQ topic. The SCDF data pipeline will be listening to the queue and will start processing.

You can also look at the logs associated with each of the components to monitor the status of the data pipeline, as shown in Figure [4-20](#526597_1_En_4_Chapter.xhtml#Fig20).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig20_HTML.png)

A screenshot depicts the details of the vehicle data pipeline. It shows the logs associated with each of the components to monitor.

Figure 4-20

Details of the VehicleDataPipeline stream

You have seen how to create a data pipeline in SCDF that monitors a topic in RabbitMQ. You created a Spring Cloud Function that posts messages into the RabbitMQ topic. Spring Cloud Function can also be deployed as a source in SCDF; more information on how to develop code for SCDF is available on the SCDF site.

## 4.3 Spring Cloud Function and AWS Glue

AWS Glue works very similarly to Spring Cloud Data Flow in that you can wire up a data pipeline that has a source, processor, and sink. More information can be found at [`https://us-east-2.console.aws.amazon.com/gluestudio/home?region=us-east-2#/`](https://us-east-2.console.aws.amazon.com/gluestudio/home%253Fregion%253Dus-east-2%2523/).

Spring Cloud Function can participate in the data pipeline process as a trigger, or simply by integrating with one of the components in the data pipeline.

For example, if you have AWS Kinesis as a source and you need to get data from a vehicle, you can have Spring Cloud Function stream the data that it gets into AWS Kinesis.

In the example in this section, you will be publishing data into AWS Kinesis and then kick off an AWS Glue job manually.

The flow will be:

Spring Cloud Function ➤ Kinesis ➤ AWS Glue Job ➤ S3

The prerequisites are:

*   Subscription to AWS, AWS Glue job, Kinesis, and S3

*   AWS Glue job with Kinesis as the source and S3 as the target

*   Code from GitHub at [`https://github.com/banup-kubeforce/Kinesis_trigger.git`](https://github.com/banup-kubeforce/Kinesis_trigger.git)

It is assumed that you have some knowledge of AWS Glue, as we do not delve into the details of this product. The focus is on creating the Spring Cloud Function.

### 4.3.1 Step 1: Set Up Kinesis

You can get to Kinesis at [`https://us-east-1.console.aws.amazon.com/kinesis/home?region=us-east-1#/home`](https://us-east-1.console.aws.amazon.com/kinesis/home%253Fregion%253Dus-east-1%2523/home)`.`

Once you subscribe to Kinesis, you can begin creating the data stream. Make sure you select on-demand (see Figure [4-21](#526597_1_En_4_Chapter.xhtml#Fig21)), as this will save you some money.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig22_HTML.png)

A screenshot depicts the active vehicle data stream under the monitoring tab.

Figure 4-22

vehicledatastream is active

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig21_HTML.jpg)

A screenshot depicts the setup for Kinesis to create a data stream for a vehicle. It shows the data stream configuration, data stream capacity and data stream settings.

Figure 4-21

Create a vehicledatastream in AWS Kinesis

You can now connect and publish to the stream using your Spring Cloud Function.

### 4.3.2 Step 2: Set Up AWS Glue

You can access the AWS Glue Studio at [`https://us-east-1.console.aws.amazon.com/gluestudio/home?region=us-east-1#/`](https://us-east-1.console.aws.amazon.com/gluestudio/home%253Fregion%253Dus-east-1%2523/).

Once you have the subscription, you can begin creating a glue job.

Go to the AWS Glue Studio to start the creation of the glue job, as shown in Figure [4-23](#526597_1_En_4_Chapter.xhtml#Fig23).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig23_HTML.png)

A screenshot depicts the creation of a job on A W S glue studio.

Figure 4-23

AWS Glue Studio, Create a Job

Create a job called `vehicledatapipeline`. Use Amazon Kinesis as the source and Amazon S3 as the target. Ensure that you set the proper configurations for each of these components.

The job shown in Figure [4-24](#526597_1_En_4_Chapter.xhtml#Fig24) will read Amazon Kinesis shards and post the data into Amazon S3.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig24_HTML.png)

A screenshot depicts the Configuration of amazon kinesis as the source and amazon S 3 integration for each component.

Figure 4-24

Configure Kinesis and S3 integration

Now you have a job in AWS Glue that you can trigger manually or via a function.

### 4.3.3 Step 3: Create a Function to Load Data into Kinesis

**1: Set the application properties.** Make sure that you provide the stream name. AWS information has to be set, as shown in Listing [4-6](#526597_1_En_4_Chapter.xhtml#PC10).

```
spring.cloud.function.definition=producerFunction
#use your aws credentials here
aws.access_key =
aws.secret_key =
aws.region = us-east-2
#use your stream name that you have created
aws.stream_name = vehicledatastream
Listing 4-6
application.properties
```

**2: Add the kinesis dependencies.** Make sure to add the necessary libraries. The latest version of producer has some bugs, so I used the working version, as shown in Listing [4-7](#526597_1_En_4_Chapter.xhtml#PC11).

```
com.amazonaws
amazon-kinesis-client
1.14.1

com.amazonaws
amazon-kinesis-producer
0.13.1

com.amazonaws
amazon-kinesis-client
1.14.1

Listing 4-7
pom.xml dependecies
```

**3:** **Create the model.** This is a simple model for tracking a vehicle’s detail. See Listing [4-8](#526597_1_En_4_Chapter.xhtml#PC12).

```
public class TrackDetail {
private String vehicleId;
private String driverId;
private String driverName;
public String getVehicleId() {
return vehicleId;
}
public void setVehicleId(String vehicleId) {
this.vehicleId = vehicleId;
}
public String getDriverId() {
return driverId;
}
public void setDriverId(String driverId) {
this.driverId = driverId;
}
public String getDriverName() {
return driverName;
}
public void setDriverName(String driverName) {
this.driverName = driverName;
}
}
Listing 4-8
TrackDetail.java
```

**4: Create the Kinesis producer.** This interface is nice to have; see Listing [4-9](#526597_1_En_4_Chapter.xhtml#PC13).

```
public interface ProducerService {
public void putDataIntoKinesis(String payload) throws Exception;
public void stop();
}
Listing 4-9
ProducerService.java
```

**5:** **Create the producer implementation.** The implementation provided in Listing [4-10](#526597_1_En_4_Chapter.xhtml#PC14) sets the method for posting to Kinesis.

```
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.kinesis.producer.*;
import com.google.common.util.concurrent.FutureCallback;
import com.google.common.util.concurrent.Futures;
import com.google.common.util.concurrent.ListenableFuture;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import java.io.UnsupportedEncodingException;
import java.nio.ByteBuffer;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicLong;
@Service
public class ProducerServiceImpl implements ProducerService {
private static final Logger LOG = LoggerFactory.getLogger(ProducerServiceImpl.class);
@Value(value = "${aws.stream_name}")
private String streamName;
@Value(value = "${aws.region}")
private String awsRegion;
@Value(value = "${aws.access_key}")
private String awsAccessKey;
@Value(value = "${aws.secret_key}")
private String awsSecretKey;
private KinesisProducer kinesisProducer = null;
// The number of records that have finished (either successfully put, or failed)
final AtomicLong completed = new AtomicLong(0);
private static final String TIMESTAMP_AS_PARTITION_KEY =
Long.toString(System.currentTimeMillis());
public ProducerServiceImpl() {
this.kinesisProducer = getKinesisProducer();
}
private KinesisProducer getKinesisProducer() {
if (kinesisProducer == null) {
BasicAWSCredentials awsCreds = new BasicAWSCredentials("AKIAZKRBTXXLPA3V2RW5", "kUqeO3bJyHEGziM09ru83/yU5vulbYagqHXmM4zG");
KinesisProducerConfiguration config = new KinesisProducerConfiguration();
config.setRegion("us-east-2");
config.setCredentialsProvider(new AWSStaticCredentialsProvider(awsCreds));
config.setMaxConnections(1);
config.setRequestTimeout(6000); // 6 seconds
config.setRecordMaxBufferedTime(5000); // 5 seconds
kinesisProducer = new KinesisProducer(config);
}
return kinesisProducer;
}
@Override
public void putDataIntoKinesis(String payload) throws Exception {
FutureCallback myCallback = new FutureCallback() {
@Override
public void onFailure(Throwable t) {
// If we see any failures, we will log them.
int attempts = ((UserRecordFailedException) t).getResult().getAttempts().size() - 1;
if (t instanceof UserRecordFailedException) {
Attempt last =
((UserRecordFailedException) t).getResult().getAttempts().get(attempts);
if (attempts > 1) {
Attempt previous = ((UserRecordFailedException) t).getResult().getAttempts()
.get(attempts - 1);
LOG.error(String.format(
"Failed to put record - %s : %s. Previous failure - %s : %s",
last.getErrorCode(), last.getErrorMessage(),
previous.getErrorCode(), previous.getErrorMessage()));
} else {
LOG.error(String.format("Failed to put record - %s : %s.",
last.getErrorCode(), last.getErrorMessage()));
}
}
LOG.error("Exception during put", t);
}
@Override
public void onSuccess(UserRecordResult result) {
long totalTime = result.getAttempts().stream()
.mapToLong(a -> a.getDelay() + a.getDuration()).sum();
LOG.info("Data writing success. Total time taken to write data = {}", totalTime);
completed.getAndIncrement();
}
};
final ExecutorService callbackThreadPool = Executors.newCachedThreadPool();
ByteBuffer data = null;
try {
data = ByteBuffer.wrap(payload.getBytes("UTF-8"));
} catch (UnsupportedEncodingException e) {
e.printStackTrace();
}
// wait until unfinished records are processed
while (kinesisProducer.getOutstandingRecordsCount() > 1e4) {
Thread.sleep(1);
}
// write data to Kinesis stream
ListenableFuture f =
kinesisProducer.addUserRecord(streamName, TIMESTAMP_AS_PARTITION_KEY, data);
Futures.addCallback(f, myCallback, callbackThreadPool);
}
@Override
public void stop() {
if (kinesisProducer != null) {
kinesisProducer.flushSync();
kinesisProducer.destroy();
}
}
}
Listing 4-10
ProducerServiceImpl.java
```

**6:** **Create the producer function.** This is the critical function that will be exposed to post data into Kinesis; see Listing [4-11](#526597_1_En_4_Chapter.xhtml#PC15).

```
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.function.Function;
public class ProducerFunction implements Function {
@Autowired
private ProducerService producerService;
@Override
public String apply(TrackDetail trackDetail) {
ObjectMapper mapper = new ObjectMapper();
String data = "";
try {
data = mapper.writeValueAsString(trackDetail);
producerService.putDataIntoKinesis(data);
} catch (JsonProcessingException e) {
e.printStackTrace();
} catch (Exception e) {
e.printStackTrace();
}
return "Saved data into Kinesis successfully!";
}
}
Listing 4-11
ProducerFunction.java
```

**7:** **Run the function**; see Figure [4-25](#526597_1_En_4_Chapter.xhtml#Fig25).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig25_HTML.png)

A screenshot depicts the successful run of the function.

Figure 4-25

Successful run of the function

**8: Test with Postman.** Run a `POST`-based test against `ProducerFunction` to publish data into Kinesis.

Add text that introduces and gives context to Figure [4-26](#526597_1_En_4_Chapter.xhtml#Fig26).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig26_HTML.jpg)

A screenshot depicts the post-based test against the producer function. It includes the tabs params, authorization, headers, body, pre-request script, tests, and settings with a button to send.

Figure 4-26

Postman test

You will get a message that the data is saved.

**9:** **Check Kinesis for data.** This information can be found on the Kinesis dashboard under Monitoring, as shown in Figure [4-27](#526597_1_En_4_Chapter.xhtml#Fig27).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig27_HTML.jpg)

A screenshot depicts the data metrics found on the kinesis dashboard.

Figure 4-27

Kinesis dashboard showing the data metrics

**10:** **Run Glue manually.** From the Glue Studio, start the process by clicking Run, as shown in Figure [4-24](#526597_1_En_4_Chapter.xhtml#Fig24).

The job starts to run, as shown in Figure [4-28](#526597_1_En_4_Chapter.xhtml#Fig28). Check the `s3` bucket for any data.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig28_HTML.png)

A screenshot depicts the glue job run from the glue studio.

Figure 4-28

The Glue job run

In this section, you learned how to create a Spring Cloud Function that can post data into AWS Kinesis that is part of the data pipeline. You learned that you can publish data into Kinesis and trigger the AWS Glue pipeline manually, but I also encourage you to explore other ways you can implement Spring Cloud Function for AWS Glue, such as creating and deploying triggers. More information on how to create AWS Glue triggers in Spring is available at [`https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-glue.xhtml`](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-glue.xhtml).

## 4.4 Spring Cloud Function and Google Cloud Dataflow

Google Cloud Dataflow is very similar to Spring Cloud Data Flow, in that it allows you to wire up a data pipeline with a source, processor, and sink. The Dataflow product is easier to develop with. You can read about Dataflow and its capabilities at [`https://cloud.google.com/dataflow`](https://cloud.google.com/dataflow).

For the example in this section, you will create a dataflow that includes cloud pub/sub:

Spring Cloud Function ➤Dataflow {Cloud Pub/Sub ➤ Cloud Storage}

Prerequisites:

*   Subscription to Google Data Flow

*   A cloud pub/sub instance

*   A cloud storage bucket

*   Code from GitHub at [`https://github.com/banup-kubeforce/GooglePubSub`](https://github.com/banup-kubeforce/GooglePubSub)

Step 1: Create and configure a cloud pub/sub instance.

Before coming to this step, ensure that you are subscribed to cloud pub/sub. Also ensure that you have proper subscriptions to the APIs.

Navigate to Cloud Pub/Sub console in your subscription to create a topic. See Figure [4-29](#526597_1_En_4_Chapter.xhtml#Fig29).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig29_HTML.png)

A screenshot depicts the creation and configuration of cloud pub or sub-instance in google cloud.

Figure 4-29

Cloud Pub/Sub with a topic

Step 2: Create and configure a bucket in cloud storage. Create a cloud storage instance and bucket. I created a bucket called `vehiclebucket1` to store the file coming from cloud pub/sub; see Figure [4-30](#526597_1_En_4_Chapter.xhtml#Fig30).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig30_HTML.jpg)

A screenshot depicts the google cloud storage with vehicle bucket 1. It includes the tabs create and refresh with a list of names under buckets.

Figure 4-30

Google Cloud Storage with vehiclebucket1

Now you are ready to build the Dataflow data pipeline.

Step 3: Create a data pipeline. Navigate to the Dataflow dashboard and create a pipeline. I created a pipeline using a prebuilt template.

**1: Pick the template**

This example uses the Pub/Sub to Text Files on Cloud Storage template, as shown in Figure [4-31](#526597_1_En_4_Chapter.xhtml#Fig31).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig31_HTML.jpg)

A screenshot of a data flow option in google cloud. It shows the template used to create the data pipeline in the drop-down and the filter option shows many data flow template formats.

Figure 4-31

Create a data pipeline from the template

**2:** **Set the parameters for pub/sub and cloud storage.**

You will be able to pick the topic that you created in Step 1 and the bucket in Step 2\. See Figure [4-32](#526597_1_En_4_Chapter.xhtml#Fig32).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig32_HTML.jpg)

A screenshot depicts the data flow parameter setup. It shows the option to create a pipeline template, data flow template, regional endpoint, input pub per subtopic, output file name prefix and temporary file location.

Figure 4-32

Complete the parameters set up for Dataflow

**3:** **Verify the creation of the data pipeline.**

You can see from Figure [4-33](#526597_1_En_4_Chapter.xhtml#Fig33) that a data pipeline has been created.

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig33_HTML.png)

A screenshot depicts the successful creation of vehicle data pipeline and its pipeline summary.

Figure 4-33

Successful creation of vehicledatapipeline

An associated job will also be created, as shown in Figure [4-34](#526597_1_En_4_Chapter.xhtml#Fig34).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig34_HTML.png)

A screenshot depicts the created job from the template in google cloud.

Figure 4-34

An associated job is created

You can find a graphical representation of the job by drilling further into the URL of the job, as shown in Figure [4-35](#526597_1_En_4_Chapter.xhtml#Fig35).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig36_HTML.png)

A screenshot depicts the vehicle bucket details. It consists of vehicle bucket 1, location, storage class, public access and protection, under the objects tab, it shows the vehicle info.

Figure 4-36

The bucket shows the posted document

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig35_HTML.png)

A screenshot depicts the graphical representation of the job. It consists of read pub-sub event, a 5-meter window and write files.

Figure 4-35

Execution of the pipeline

Step 4: Create the Spring Cloud Function.

In this Spring Cloud Function, you create the following classes:

*   `MessageEntity` class to formulate the message

*   `TrackDetail` class as the entity class

*   `PubSubPublisher` class that subscribes to the topic and publishes data

*   `ProducerFunction` class to implement the Spring Cloud Function

Prerequisites:

*   Maven dependencies

You need to add `spring-cloud-gcp-starter-pubsub` and `spring-integration-core` in addition to `spring-cloud-function-web`; see Listing [4-12](#526597_1_En_4_Chapter.xhtml#PC16).

```

org.springframework.boot
spring-boot-starter-web

org.springframework.cloud
spring-cloud-function-web

com.google.cloud
spring-cloud-gcp-starter-pubsub
3.3.0

org.springframework.integration
spring-integration-core

org.springframework.boot
spring-boot-starter-test
test

Listing 4-12
Maven Dependencies
```

The `Application.properties` file is shown in Listing [4-13](#526597_1_En_4_Chapter.xhtml#PC17).

```
spring.cloud.function.definition=producerFunction
spring.cloud.gcp.project-id=springcf-348721
spring.cloud.gcp.credentials.location=file:C://Users//banua//Downloads//application_default_credentials.json
pubsub.topic=projects/springcf-348721/topics/vehicletopic
Listing 4-13
application.properties
```

**1: Create the** `MessageEntity` **class****.**

Store the message with a timestamp, as shown in Listing [4-14](#526597_1_En_4_Chapter.xhtml#PC18).

```
package com.kubeforce.googlepubsub;
import java.time.LocalDateTime;
public class MessageEntity {
private final LocalDateTime timestamp;
private final String message;
public MessageEntity(LocalDateTime timestamp, String message) {
this.timestamp = timestamp;
this.message = message;
}
public LocalDateTime getTimestamp() {
return timestamp;
}
public String getMessage() {
return message;
}
}
Listing 4-14
MessageEntity.java
```

**2:** **Create the** `TrackDetail` **class.**

The `TrackDetail` class will have three fields—`vehicleId`, `driverId`, and `driverName`; see Listing [4-15](#526597_1_En_4_Chapter.xhtml#PC19).

```
package com.kubeforce.googlepubsub;
public class TrackDetail {
private String vehicleId;
private String driverId;
private String driverName;
public String getVehicleId() {
return vehicleId;
}
public void setVehicleId(String vehicleId) {
this.vehicleId = vehicleId;
}
public String getDriverId() {
return driverId;
}
public void setDriverId(String driverId) {
this.driverId = driverId;
}
public String getDriverName() {
return driverName;
}
public void setDriverName(String driverName) {
this.driverName = driverName;
}
Listing 4-15
TrackDetail.java
```

**3: Create the** `PubSubPublisher`**.**

The `PubSubPublisher` will use the topic defined in `application.properties` to send messages; see Listing [4-16](#526597_1_En_4_Chapter.xhtml#PC20).

```
package com.kubeforce.googlepubsub;
import com.google.cloud.spring.pubsub.core.PubSubTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
@Component
public class PubSubPublisher {
private final String topic;
private final PubSubTemplate pubSubTemplate;
public PubSubPublisher(
@Value(“${pubsub.topic}”) String topic,
PubSubTemplate pubSubTemplate) {
this.topic = topic;
this.pubSubTemplate = pubSubTemplate;
}
public void publish(String payload) {
pubSubTemplate.publish(topic, payload);
}
}
Listing 4-16
PubSubPublisher.java
```

**4:** **Create the Spring Cloud Function.**

The `ProducerFunction` will use the topic defined in `application.properties` to send messages. See Listing [4-17](#526597_1_En_4_Chapter.xhtml#PC21).

```
package com.kubeforce.googlepubsub;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import java.time.LocalDateTime;
import java.util.function.Function;
public class ProducerFunction implements Function {
@Autowired
private PubSubPublisher publisher;
@Override
public String apply(TrackDetail trackDetail) {
ObjectMapper mapper = new ObjectMapper();
String data = “”;
try {
data = mapper.writeValueAsString(trackDetail);
MessageEntity entity = new MessageEntity(LocalDateTime.now(), data);
publisher.publish(data);
} catch (JsonProcessingException e) {
e.printStackTrace();
} catch (Exception e) {
e.printStackTrace();
}
return "Saved data into Kinesis successfully!";
}
}
Listing 4-17
ProducerFunction.java
```

**5:** **Run the application and test if a message is published to Cloud Pub/Sub.**

Go to the Pub/Sub console in your Google Cloud console and verify that the message has been posted; see Figure [4-37](#526597_1_En_4_Chapter.xhtml#Fig37).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig37_HTML.png)

A screenshot depicts the message posted in the pub per sub console in google cloud.

Figure 4-37

The message has been posted in Cloud Pub/Sub

**6: Verify is the message has been loaded into Cloud Storage.**

Navigate to the Cloud Storage console in your Google Cloud to verify that the message has been loaded; see Figure [4-38](#526597_1_En_4_Chapter.xhtml#Fig38).

![](images/526597_1_En_4_Chapter/526597_1_En_4_Fig38_HTML.jpg)

A screenshot depicts the cloud storage console to verify that the message has been loaded into the storage. It shows the bucket details of vehicle bucket one.

Figure 4-38

The message is loaded into Cloud Storage

In this section, you learned how to use Spring Cloud Function to trigger a Google Cloud Dataflow-based data pipeline.

## 4.5 Summary

This chapter explained how to create dataflow and data pipelines, whether on-premises using SCDF or in the cloud. For the cloud, you can use SCDF or cloud-native tools.

Spring Cloud Function is versatile and can be used in the context of data pipelines as a trigger or as part of the flow.

With AWS Glue and Google Data Flow, you saw that you can use Spring Cloud Function as a trigger for the flows. This requires some additional coding by adding some relevant libraries and invoking the flow.

Upcoming chapters discuss other use cases of Spring Cloud Function.

# 5. AI/ML Trained Serverless Endpoints with Spring Cloud Function

This chapter looks at how Spring Cloud Function can be leveraged in AI/ML. You learn about the AI/ML process and learn where Spring Cloud Function fits in the process. You also learn about some of the offerings from the cloud providers, such as AWS, Google, and Azure.

Before delving into the details of Spring Cloud Function implementation, you need to understand the AI/ML process. This will set the stage for implementing Spring Cloud Function.

## 5.1 AI/ML in a Nutshell

AI/ML is gaining popularity, as it is being offered by almost all cloud providers. For AI/ML to work properly, it is important to understand the process behind it. See Figure [5-1](#526597_1_En_5_Chapter.xhtml#Fig1).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig1_HTML.jpg)

A process of the M L lifecycle. The 4 processing steps are, gathering requirements, setting A I slash M L pipeline, performing A I slash M L, and monitoring.

Figure 5-1

ML lifecycle

Let’s dig deeper into the process depicted in Figure [5-1](#526597_1_En_5_Chapter.xhtml#Fig1) and see what is accomplished.

1.  Gathering requirements
    *   Model requirements

        This is an important step in the AI/ML process. This determines the ultimate success or failure of the AI/ML model activity. The requirements for models must match the business objectives.

    *   What is the return on investment (ROI) expected from this activity?

    *   What are the objectives? Examples may include reduce manufacturing costs, reduce equipment failures, or improve operator productivity.

    *   What are the features that need to be included in the model?

    *   In character recognition, it can be histograms counting the number of black pixels along horizontal and vertical directions, the number of internal holes, and so on.

    *   In speech recognition, it can be recognizing phonemes.

    *   In computer vision, it can be a lot of features such as objects, edges, shape size, depth, and so on.

*   Variance in deployed data

*   Variance refers to the sensitivity of the learning algorithms to the training dataset. Every time you try to fit a model, the output parameters may vary ever so slightly, which will alter the predictions. In a production environment where the model has been deployed, these variances may have a significant impact if they’re not corrected in time.

*   Changes in data integrity

*   Machine learning data is dynamic and requires tweaking to ensure the right data is supplied to the model. There are three types of data integrity problems—missing values, range violations, and type mismatches. Constant monitoring and management of these types of issues is important for a good operational ML.

*   Data drift

*   Data drift occurs when the training dataset does not match the data output in production.

*   Concept drift

*   Concept drift is the change in relationships between input and output data over time. For example, when you are trying to predict consumer purchasing behavior, the behavior may be influenced by factors other than what you specified in the model. factors that are not explicitly used in the model prediction are called hidden contexts.

1.  Setting up the data pipeline
    *   Data collection
        1.  What datasets to integrate

        2.  What are the sources

        3.  Are the datasets available

    *   Data cleaning: This activity involves removing inaccurate or noisy records from the dataset. This may include fixing spelling and syntax errors, standardizing datasets, removing empty fields, and removing duplicate data. 45 percent of a data scientist’s time is spent on cleaning data ([`https://analyticsindiamag.com/data-scientists-spend-45-of-their-time-in-data-wrangling/`](https://analyticsindiamag.com/data-scientists-spend-45-of-their-time-in-data-wrangling/)).

    *   Data labeling: Tagging or labeling raw data such as images, videos, text, audio, and so on, is an important part of the AI/ML activity. This makes the data meaningful and allows the machine learning model to identify a particular class of objects. This helps a lot in the supervised learning activities such as image classification, image segmentation, and so on.

2.  Performing the AI/ML tasks
    *   Feature engineering: This refers to all activities that are performed to extract and select features for machine learning models. This includes the use of domain knowledge to select and transform the most relevant variables from raw data to create predictive models. The goal of feature engineering is to improve the performance of machine learning algorithms. The success or failure of the predictive model is determined by feature engineering and ensure that the model will be comprehensible to humans.

    *   Train model: In this step the machine learning algorithm is fed with sufficient training data to learn from. The training model dataset consists of sample output data and the corresponding sets of input data that influence the output. This is a iterative process that takes the input data through the algorithm and correlates it against the sample output. The result is then used to modify the model. This iterative process is called “model fitting” until the model precision meets the goals.

    *   Model evaluation: This process involves using metrics to understand the model’s performance, including its strengths and weaknesses. For example, doing a classification prediction, the metrics can include true positives, true negatives, false positives, and false negatives. Other derived metrics can be accuracy, precision, and recall. Model evaluation allows you to determine how well the model is doing, the usefulness of the model, how additional model training will improve performance, and whether you should include more features.

    *   Deploy model: This process involves deploying a model to a live environment. These models can then be exposed to other processes through the method of model serving. The deployment of models can involve a process of storing the models in a store such as Google Cloud Storage.

3.  Monitoring the AI/ML models

    In this process, you want to make sure that the model is working properly and that the model predictions are effective. The reason you need to monitor model is that models may degrade over time due to these factors:

Let’s evaluate these activities from a compute perspective. This will allow us to determine what kind of compute elements we can assign to these process

Some of the activities in this process are short lived and some are long running process. For example, deploying models and accessing the deployed models is a short lived process. While Training models and model evaluation require both a manual and programmatic intervention and will take a lot of processing time.

Table [5-1](#526597_1_En_5_Chapter.xhtml#Tab1) shows the type of compute that can be applied to the processes. Some of the processes are manual.

Table 5-1

Where to Use Spring Cloud Function in the AI/ML Process

| AI/ML Process | Human | Compute |   |
| --- | --- | --- | --- |
|   |   | **Spring Cloud Function (Short Run)** | **Batch (Long Run)** |
| Model requirements | Human/manual process |   |   |
| Collect data |   | Integration triggers, data pipeline sources or sinks | Data pipeline process-Transformation |
| Data cleaning |   | Integration triggers | Transformation process |
| Data labeling |   | Tagging discrete elements-updates, deletes | Bulk tagging |
| Feature engineering | Manual |   |   |
| Train model |   | Trigger for training | Training process |
| Model evaluation | Manual | Triggers for evaluation | Bulk evaluation |
| Deploy models |   | Model serving, model | Bulk storage |
| Monitoring models |   | alerts |   |

AI/ML processes require varying compute and storage requirements. Depending on the model size, the time taken to train, the complexity of the model, and so on, the process may require different compute and storage at different times. So, the environment should be scalable. In earlier days, AI/ML activities were conducted with a fixed infrastructure, through over-allocated VMs, dedicated bare metal servers, or parallel or concurrent processing units. This made the whole process costly and it was left to companies with deep pockets to be able to conduct proper AI/ML activities.

Today, with all the cloud providers providing some level of AI/ML activities through an API or SaaS approach, and with the ability to pay per use or pay as you go, companies small and big have begun to utilize AI/ML in their compute activities.

Paradigms such as cloud functions make it even easier to take advantage of a scalable platform offered by the cloud. Activities such as model storage and retrieval can be done on demand with cloud functions. Serving pre-trained models is easy through cloud functions and these models can be made available to any client without the need to install client libraries. Here are some of the advantages of cloud functions in AI/ML:

*   Codeless inference makes getting started easy

*   Scalable infrastructure

*   No management of infrastructure required

*   Separate storage for the model, which is very convenient for tracking versions of the model and for comparing their performance

*   Cost structure allows you to pay per use

*   Ability to use different frameworks

### 5.1.1 Deciding Between Java and Python or Other Languages for AI/ML

Most of the popular frameworks such as TensorFlow are written in Python, so the models’ outputs are also Python based. Therefore, it’s easy for anyone working on AI/ML to code in Python. See Figure [5-2](#526597_1_En_5_Chapter.xhtml#Fig2).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig2_HTML.jpg)

A pictorial horizontal bar graph of the most popular programming languages. It mentions the logo of worldwide languages with percentages and a laptop image. Python has the highest at 25.95 and Ruby, has the lowest at 1.53.

Figure 5-2

AI/ML language popularity

It is very important to understand that the popularity of a language does not equate to it being a good, robust, secure language for use in AI/ML.

There are several reasons to choose Java over Python or R:

*   Enterprises have standardized on Java, so they prefer to have their AI/ML platform written in Java to ease the integration into existing systems.

*   Apache.org , the open source community for Java, is very robust and has many libraries and tools that have been tuned toward speed of compute, data processing, and so on. Tools such as Hadoop, Hive, and Spark are integral to the AI/ML process. Developers can easily use these tools and libraries in their java code.

*   Java can be used at all touchpoints in the AI/ML process, including data collection, cleansing, labeling, model training, and so on. This way you can standardize on one language for AI/ML needs.

*   JVMs allow for applications to be portable across different machine types.

*   Due to Java’s object-oriented mechanisms and JVMs, it is easier to scale.

*   Java-based computation for AI/ML can be made to perform faster with some tuning at the algorithm and JVM level. Therefore, it is a preferred language for sites like Twitter, Facebook, and so on.

*   Java is a strong typing programming language, meaning developers must be explicit and specific about variables and types of data.

*   Finally, production codebases are often written in Java. If you want to build an enterprise-grade application, Java is the preferred language.

Since Java is a preferred enterprise language for AI/ML, we can safely say that Spring Cloud Function is a better framework to use when developing enterprise-grade functions for AI/ML.

This chapter explores the different offerings from the different cloud providers and explains how you can use Spring Cloud Function with these offerings.

## 5.2 Spring Framework and AI/ML

A lot of frameworks have been developed in Java that can be leveraged using the Spring Framework. The latest of these frameworks was developed by AWS and is called DJL (Deep Java Library). This library can integrate with PyTorch, TensorFlow, Apache MXNet, ONNX, Python, and TFLite based models.

One of the important capabilities that you need is model serving, where you can leverage Spring Cloud Function to serve trained models, and DJL provides this capability out of the box. It’s called djl-serving.

Spring Cloud Function is unique in its ability to transcend the on-premises and cloud, especially in the realm of AI/ML. Even though cloud has become popular, not all companies have fully transitioned to the cloud. Most of them in fact have adopted a hybrid approach. Some of the applications and data still reside in the company-owned datacenters or are co-hosted in datacenters operated by third-party service providers. AI/ML activities revolving the data that is residing in the datacenters will need to have models that are trained and stored and be served using cloud functions that can be hosted in the datacenter. Cloud functions hosted in the datacenter are nearer to their data and therefore have better performance than cloud functions that are hosted in the cloud and access models that are trained and stored in the on-premises datacenters. This is where Spring Cloud Function can help serve models on-premises. See Figure [5-3](#526597_1_En_5_Chapter.xhtml#Fig3).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig3_HTML.jpg)

An illustrated model of the on-prem data center. The flow of data includes trained model data, the model serving, an enterprise application, a firewall, and an external application.

Figure 5-3

On-premises and Spring Cloud Function deployment for model serving

## 5.3 Model Serving with Spring Cloud Function with DJL

Before you explore the cloud provider’s option, it’s a good idea try this out locally. To do that, you need a framework installed and access to a good tensor model and an image. The framework that you use in this example is called `djl-serving`.

### 5.3.1 What Is DJL?

Deep Java Library (DJL) [`https://docs.djl.ai/`](https://docs.djl.ai/) is a high-level, engine-agnostic Java framework for deep learning. It allows you to connect to any framework like TensorFlow or PyTorch and conduct AI/ML activities from Java.

DJL has also great hooks to Spring Boot and can easily be invoked through the Spring Framework. DJL acts as an abstraction layer across frameworks and makes it easy to interact with those frameworks, as shown in Figure [5-4](#526597_1_En_5_Chapter.xhtml#Fig4).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig4_HTML.jpg)

A model of layers of the deep java library. It includes a java framework with a logo of spring boot, and spring cloud function. An M L framework of m x net, Tensor flow, PyTorch. Deep Lava Library is mentioned between the frameworks.

Figure 5-4

Deep Java Library (DJL) layers

There are many components in DJL that are useful to look at, but the DJL serving is interesting.

Run the following commands to get the `djl-serving` bits. Then unzip the file into your directory of choice and set the path to the `serving.bat` located at `~\serving-0.19.0\bin\serving.bat`. This will allow you to execute serving from anywhere on your machine.

```
curl -O https://publish.djl.ai/djl-serving/serving-0.19.0.zip
unzip serving-0.19.0.zip
```

Listing [5-1](#526597_1_En_5_Chapter.xhtml#PC2) shows a sample run of `djl-serving` with a TensorFlow model.

```
# Load a TensorFlow model from TFHub
C:\Users\banua>serving -m "resnet=https://tfhub.dev/tensorflow/resnet_50/classification/1"
[INFO ] - Starting djl-serving: 0.19.0 ...
[INFO ] -
Model server home: C:\Users\banua
Current directory: C:\Users\banua
Temp directory: C:\Users\banua\AppData\Local\Temp\
Command line:
Number of CPUs: 16
Max heap size: 8114
Config file: N/A
Inference address: http://127.0.0.1:8080
Management address: http://127.0.0.1:8080
Default job_queue_size: 1000
Default batch_size: 1
Default max_batch_delay: 300
Default max_idle_time: 60
Model Store: N/A
Initial Models: resnet=https://tfhub.dev/tensorflow/resnet_50/classification/1
Initial Workflows: N/A
Netty threads: 0
Maximum Request Size: 67108864
[INFO ] - Initializing model: resnet=https://tfhub.dev/tensorflow/resnet_50/classification/1
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-synch-l1-2-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-file-l1-2-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/THIRD_PARTY_TF_JNI_LICENSES.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-file-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-environment-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-synch-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-string-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-memory-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/msvcp140.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-util-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-datetime-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/vcruntime140.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/concrt140.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-sysinfo-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/ucrtbase.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-interlocked-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-processenvironment-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-file-l2-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/tensorflow_cc.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/libiomp5md.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/vcomp140.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-timezone-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/jnitensorflow.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-convert-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-errorhandling-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-namedpipe-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-math-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-locale-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-heap-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-profile-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/LICENSE.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-utility-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-heap-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-localization-l1-2-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-debug-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-processthreads-l1-1-1.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-libraryloader-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-time-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-rtlsupport-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-runtime-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-stdio-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-console-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/vcruntime140_1.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-processthreads-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-core-handle-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-filesystem-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-multibyte-l1-1-0.dll.gz ...
[INFO ] - Downloading https://publish.djl.ai/tensorflow-2.7.0/win/cpu/api-ms-win-crt-string-l1-1-0.dll.gz ...
2022-09-21 12:09:38.465035: I external/org_tensorflow/tensorflow/core/platform/cpu_feature_guard.cc:151] This TensorFlow binary is optimized with oneAPI Deep Neural Network Library (oneDNN) to use the following CPU instructions in performance-critical operations:  AVX2
To enable them in other operations, rebuild TensorFlow with the appropriate compiler flags.
[INFO ] - initWorkers for resnet (cpu()): -1, -1
[INFO ] - Loading model resnet on cpu()
2022-09-21 12:09:38.595923: I external/org_tensorflow/tensorflow/cc/saved_model/reader.cc:43] Reading SavedModel from: C:\Users\banua\.djl.ai\cache\repo\model\undefined\ai\djl\localmodelzoo\ffdb59c80e9d66dc0ce00e409e06e710
2022-09-21 12:09:38.641647: I external/org_tensorflow/tensorflow/cc/saved_model/reader.cc:107] Reading meta graph with tags { serve }
2022-09-21 12:09:38.641933: I external/org_tensorflow/tensorflow/cc/saved_model/reader.cc:148] Reading SavedModel debug info (if present) from: C:\Users\banua\.djl.ai\cache\repo\model\undefined\ai\djl\localmodelzoo\ffdb59c80e9d66dc0ce00e409e06e710
2022-09-21 12:09:38.837590: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:210] Restoring SavedModel bundle.
2022-09-21 12:09:39.330251: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:194] Running initialization op on SavedModel bundle at path: C:\Users\banua\.djl.ai\cache\repo\model\undefined\ai\djl\localmodelzoo\ffdb59c80e9d66dc0ce00e409e06e710
2022-09-21 12:09:39.746608: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:283] SavedModel load for tags { serve }; Status: success: OK. Took 1150043 microseconds.
[INFO ] - scanning for plugins...
[INFO ] - plug-in folder not exists:C:\Users\banua\plugins
[INFO ] - 0 plug-ins found and loaded.
[INFO ] - Initialize BOTH server with: NioServerSocketChannel.
[INFO ] - BOTH API bind to: http://127.0.0.1:8080
[INFO ] - Model server started.
Listing 5-1
djl-serving Run with a Tensorflow Model
```

On the initial run, the model you specified will be loaded:

```
"resnet=https://tfhub.dev/tensorflow/resnet_50/classification/1"
```

On subsequent runs, the model server will be available at port 8080 at `http://localhost:8080`.

This example provides an image of kitten and it will try to recognize the kitten by providing output with probabilities:

```
$curl -O https://resources.djl.ai/images/kitten.jpg
```

This will show the image in Figure [5-5](#526597_1_En_5_Chapter.xhtml#Fig5).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig5_HTML.jpg)

A photograph of a kitten with closed eyes and legs laid out.

Figure 5-5

Image of a kitten for the model to predict

Next, run the following and you will see the output with probabilities.

You provide the djl-`serving` instance that is running at `http://localhost:8080/predictions` with the kitten image that is located in the current directory, and you get a response shown in Figure [5-6](#526597_1_En_5_Chapter.xhtml#Fig6), which shows that the image is probably a tabby cat. The probability is 0.4107377231121063\. This is close.

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig7_HTML.png)

An X-ray image. It diagnosis the lungs using a saved model.

Figure 5-7

Xray image provided to the saved_model

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig6_HTML.jpg)

A screenshot of the window. It has the class name and probability input and outputs. It represents the D J L results.

Figure 5-6

DJL results for the image

Next, you see how you can use DJL to create a Spring Cloud Function to serve models.

### 5.3.2 Spring Cloud Function with DJL

For this example, we borrow an example from DJL called pneumonia detection. This sample is available at [`https://github.com/deepjavalibrary/djl-demo/tree/master/pneumonia-detection`](https://github.com/deepjavalibrary/djl-demo/tree/master/pneumonia-detection).

This example uses an Xray image from [`https://djlai.s3.amazonaws.com/resources/images/chest_xray.jpg`](https://djlai.s3.amazonaws.com/resources/images/chest_xray.jpg).

It predicts using a model from [`https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip`](https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip).

The Spring Cloud Function you create will take an image, load the model, and provide a prediction, as in the cat example.

Prerequisites:

*   DJL libraries

*   A model: [`https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip`](https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip)

*   The URL of the image to analyze: [`https://djl-ai.s3.amazonaws.com/resources/images/chest_xray.jpg`](https://djl-ai.s3.amazonaws.com/resources/images/chest_xray.jpg)

Step 1: Create the Spring Cloud Function with DJL framework. Add dependencies to the Hadoop file.

Add the DJL highlighted dependencies along with `spring-cloud-function-web` and GCP dependencies, as shown in Listing [5-2](#526597_1_En_5_Chapter.xhtml#PC5).

```

org.springframework.boot
spring-boot-starter-web

org.springframework.cloud
spring-cloud-function-web

ai.djl
bom
0.12.0
pom

ai.djl
api
0.12.0

ai.djl.tensorflow
tensorflow-api
0.12.0

ai.djl.tensorflow
tensorflow-engine
0.12.0

ai.djl.tensorflow
tensorflow-native-auto
2.4.1
runtime

org.projectlombok
lombok
true

Listing 5-2
Dependencies for DJL
```

Step 2: Create the Spring Cloud Function.

Now create an `XRAYFunction` that stores a model from the URL provided: [`https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip`](https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip). See Listing [5-3](#526597_1_En_5_Chapter.xhtml#PC6).

```
package com.kubeforce.djlxray;
import ai.djl.inference.Predictor;
import ai.djl.modality.Classifications;
import ai.djl.modality.cv.Image;
import ai.djl.modality.cv.ImageFactory;
import ai.djl.modality.cv.translator.ImageClassificationTranslator;
import ai.djl.modality.cv.util.NDImageUtils;
import ai.djl.repository.zoo.Criteria;
import ai.djl.repository.zoo.ZooModel;
import ai.djl.translate.Translator;
import lombok.SneakyThrows;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.io.IOException;
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.function.Function;
public class XRAYFunction implements Function, String> {
private static final Logger logger = LoggerFactory.getLogger(XRAYFunction.class);
private static final List CLASSES = Arrays.asList("Normal", "Pneumonia");
String imagePath;
String savedModelPath;
@SneakyThrows
@Override
public String apply(Map imageinput) {
imagePath= imageinput.get("url");
savedModelPath = imageinput.get("savedmodelpath");
Image image;
try {
image = ImageFactory.getInstance().fromUrl(imagePath);
} catch (IOException e) {
throw new RuntimeException(e);
}
Translator translator =
ImageClassificationTranslator.builder()
.addTransform(a -> NDImageUtils.resize(a, 224).div(255.0f))
.optSynset(CLASSES)
.build();
Criteria criteria =
Criteria.builder()
.setTypes(Image.class, Classifications.class)
//                           .optModelUrls("https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip")
.optModelUrls(savedModelPath)
.optTranslator(translator)
.build();
try (ZooModel model = criteria.loadModel();
Predictor predictor = model.newPredictor()) {
Classifications result = predictor.predict(image);
logger.info("Diagnose: {}", result);
return result.toJson();
}
}
}
Listing 5-3
XRAYFunction.java
```

Step 3: Test locally. Run the Spring Cloud Function and invoke the endpoint `http://localhost:8080/xrayFunction`

Provide input:

```
{
"url":"https://djl-ai.s3.amazonaws.com/resources/images/chest_xray.jpg",
"savedmodelpath":https://djl-ai.s3.amazonaws.com/resources/demo/pneumonia-detection-model/saved_model.zip
}
```

This is executed in Postman, as shown in Figure [5-8](#526597_1_En_5_Chapter.xhtml#Fig8).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig8_HTML.jpg)

A screenshot of the postman testing. It highlights the local host, body, and row options with a testing program.

Figure 5-8

Testing with a POST in Postman

Upon invoking the function, the model is downloaded and then loaded into memory. This takes about a minute to load, after which it comes back with a successful message. The model took 802066 microseconds (80 seconds) to load, and this is critical for your function calls, as you will have to accommodate for this model-loading time. See Figure [5-9](#526597_1_En_5_Chapter.xhtml#Fig9).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig10_HTML.jpg)

A flow diagram of the saved model. It classifies as training and deployment. It represents the tensor flow components. The output from the training is sent to the saved model and further to the deployment.

Figure 5-10

TensorFlow components^([¹](#fn1))

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig9_HTML.png)

A screenshot of programming functions with number, date, and time. It represents the results from image evaluation.

Figure 5-9

Prediction results from the image evaluation

This section successfully demonstrated that Spring Cloud Function can act as a model server in AI/ML. This is a critical function, as you can move the loading and serving of models from traditional servers to a function-based, “pay-per-use” model.

You also learned how to use deep learning Java libraries in your functions. You can deploy this Spring Cloud Function to any cloud, as shown in Chapter [2](#526597_1_En_2_Chapter.xhtml).

## 5.4 Model Serving with Spring Cloud Function with Google Cloud Functions and TensorFlow

This section explores the model serving on Google. It uses TensorFlow, which is a Google product from AI/ML and explains how to build and save an AI model with datasets such as MNIST (`https://en.wikipedia.org/wiki/MNIST_database`).

### 5.4.1 TensorFlow

TensorFlow was developed by Google and is an open source platform for machine learning. It is an interface for expressing and executing machine learning algorithms. The beauty of TensorFlow is that a model expressed in TensorFlow can be executed with minimal changes on mobile devices, laptops, or large-scale systems with multiple GPUs and CPUs. TensorFlow is flexible and can express a lot of algorithms, including training and inference algorithms for deep neural networks, speech recognition, robotics, drug discovery, and so on.

In Figure [5-9](#526597_1_En_5_Chapter.xhtml#Fig9), you can see that TensorFlow can be deployed to multiple platforms and has many language interfaces. Unfortunately, TensorFlow is written in Python, so most of the models are written and deployed in Python. This poses a unique challenge for enterprises who have standardized on Java.

Even though TensorFlow is written in Python, there are lots of frameworks written in Java that work on a saved model.

Let’s look at how you can work with TensorFlow on the Google Cloud platform.

Google Cloud provides different approaches to working on models. You can use a container- or Kubernetes-based approach, an SaaS-based approach, or a Cloud Functions-based approach. Each has advantages and disadvantages. Google has published a good guide for you to pick the right platform for your needs, as shown in Table [5-2](#526597_1_En_5_Chapter.xhtml#Tab2).

Table 5-2

Google and AI/ML Environment^([²](#fn2))

| ![](images/526597_1_En_5_Chapter/526597_1_En_5_Figa_HTML.jpg)A table with 4 columns and 6 rows. The column headers are the features, compute engine, A L platform prediction, and cloud functions. It categorizes features and descriptions. |

As you can see from Table [5-2](#526597_1_En_5_Chapter.xhtml#Tab2), cloud functions are recommended for experimentation. Google recommends Compute Engine with TF Serving, or its SaaS platform (AI Platform) for predictions for production deployments.

The issue with this approach is that a function-based approach is more than just an experimentation environment. Functions are a way of saving on cost while exposing the serving capabilities for predictions through APIs. It is a serverless approach, so enterprises do not have to worry about scaling.

### 5.4.2 Example Model Training and Serving

In this section you see how to train an AI model locally and upload it to Google Cloud Storage. You will then download the model and test an image through a Cloud Function API. You will use a model that is based on MNIST. More about MNIST can be found at `https://en.wikipedia.org/wiki/MNIST_database`.

In this example, you develop the model in Python and then expose the model through Spring Cloud Function using DJL; see Figure [5-11](#526597_1_En_5_Chapter.xhtml#Fig11).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig11_HTML.jpg)

A model of spring cloud function. It indicates the tensor flow of H T T P J SON request, cloud storage, and cloud function with two logos of spring cloud function, and deep java library.

Figure 5-11

Spring Cloud Function with DJL and TensorFlow

You use the same example outlined at `https://cloud.google.com/blog/products/ai-machine-learning/how-to-serve-deep-learning-models-using-tensorflow-2-0-with-cloud-functions`. This will allow you to concentrate more on the Spring Cloud Function code that you will be creating rather than the actual implementation in Python.

You will then serve the model using Spring Cloud Function.

Step 1: Install TensorFlow.

```
python3 -m pip install tensorflow
```

Step 2: Create a project. Create a project called `MNIST` and then create a `main.py` file with the code in Listing [5-4](#526597_1_En_5_Chapter.xhtml#PC10). I used PyCharm to run this code.

On your Mac, make sure to run this command before running the code; otherwise you will get a certificate error. The code tries to download packages from `googleapis`:

```
open /Applications/Python\ 3.7/Install\ Certificates.command
```

I ran `main.py` and the whole process took me 13 minutes. At the end of it, I was able to get two model files as output.

```
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function
from __future__ import unicode_literals
import tensorflow as tf
from tensorflow.keras.layers import Dense, Flatten, Conv2D
from tensorflow.keras import Model
EPOCHS = 10
mnist = tf.keras.datasets.mnist
fashion_mnist = tf.keras.datasets.fashion_mnist
(x_train, y_train), (x_test, y_test) = fashion_mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0
# Add a channels dimension e.g. (60000, 28, 28) => (60000, 28, 28, 1)
x_train = x_train[..., tf.newaxis]
x_test = x_test[..., tf.newaxis]
train_ds = tf.data.Dataset.from_tensor_slices(
(x_train, y_train)).shuffle(10000).batch(32)
test_ds = tf.data.Dataset.from_tensor_slices((x_test, y_test)).batch(32)
class CustomModel(Model):
def __init__(self):
super(CustomModel, self).__init__()
self.conv1 = Conv2D(32, 3, activation='relu')
self.flatten = Flatten()
self.d1 = Dense(128, activation='relu')
self.d2 = Dense(10, activation='softmax')
def call(self, x):
x = self.conv1(x)
x = self.flatten(x)
x = self.d1(x)
return self.d2(x)
model = CustomModel()
loss_object = tf.keras.losses.SparseCategoricalCrossentropy()
optimizer = tf.keras.optimizers.Adam()
train_loss = tf.keras.metrics.Mean(name='train_loss')
train_accuracy = tf.keras.metrics.SparseCategoricalAccuracy(name='train_accuracy')
test_loss = tf.keras.metrics.Mean(name='test_loss')
test_accuracy = tf.keras.metrics.SparseCategoricalAccuracy(name='test_accuracy')
@tf.function
def train_step(images, labels):
with tf.GradientTape() as tape:
predictions = model(images)
loss = loss_object(labels, predictions)
gradients = tape.gradient(loss, model.trainable_variables)
optimizer.apply_gradients(zip(gradients, model.trainable_variables))
train_loss(loss)
train_accuracy(labels, predictions)
@tf.function
def test_step(images, labels):
predictions = model(images)
t_loss = loss_object(labels, predictions)
test_loss(t_loss)
test_accuracy(labels, predictions)
for epoch in range(EPOCHS):
for images, labels in train_ds:
train_step(images, labels)
for test_images, test_labels in test_ds:
test_step(test_images, test_labels)
template = 'Epoch {}, Loss: {}, Accuracy: {}, Test Loss: {}, Test Accuracy: {}'
print(template.format(epoch + 1,
train_loss.result(),
train_accuracy.result() * 100,
test_loss.result(),
test_accuracy.result() * 100))
# Save the weights
model.save_weights('fashion_mnist_weights')
tf.saved_model.save(model, export_dir="c://Users//banua//Downloads/MNIST/models")
Listing 5-4
main.py
```

The key is `tf.saved_model.save(model, export_dir="c://Users//banua//Downloads/MNIST/models")`.

This will save the model so that any model server can use it.

Step 3: Run the project.

Execute `main.py` from the IDE. See Figure [5-12](#526597_1_En_5_Chapter.xhtml#Fig12).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig12_HTML.png)

A screenshot of the window. It represents the successful run of M N I S T. The pop-up text reads as generated model files located in the root of the project.

Figure 5-12

Successful run of MNIST and model building

Zip the assets, variables, and the `saved_model.pb.` file as `Savedmodel3.zip` and upload it to the Google Cloud Storage.

Step 4: Upload the models into Cloud Storage. Navigate to your Google Cloud Console and subscribe to Cloud Storage. It is available at `cloud.google.com`.

Create a storage bucket in Google Cloud Storage and upload the two files into the storage bucket. Use the defaults for this example. If you are using free credits from Google, this storage should be covered.

I created a bucket called `mnist-soc`. You will use the bucket name in the Cloud Functions call. See Figure [5-13](#526597_1_En_5_Chapter.xhtml#Fig13).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig13_HTML.jpg)

A screenshot of the Google cloud storage bucket. It includes the name of the bucket, choosing store data, choosing default storage, choosing control access, and protecting access with highlighted lines.

Figure 5-13

Google Cloud Storage Bucket creation steps

Name your bucket `mnist-soc` and leave the others set to the defaults; then click Create.

Upload the `savedmodel3.zip` file to this folder by clicking Upload Files. See Figure [5-14](#526597_1_En_5_Chapter.xhtml#Fig14).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig14_HTML.jpg)

A screenshot of a mnist soc application. It highlights the object option and mentions the model's name, size, and type. It represents the models located in cloud storage.

Figure 5-14

Models deployed into Cloud Storage

Click the file to get the details of the URL you need to connect to, as shown in Figure [5-15](#526597_1_En_5_Chapter.xhtml#Fig15).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig15_HTML.png)

A screenshot represents the U R L for a model. It indicates a mnist soc application with a live object option. It highlights the public and authentication U R L with a link.

Figure 5-15

URL for the savedmodel3.zip

The URL you use for testing this example is [`https://storage.googleapis.com/mnist-soc/savedmodel3.zip`](https://storage.googleapis.com/mnist-soc/savedmodel3.zip).

The test image you use for this example is [`https://storage.googleapis.com/mnist-soc/test.png`](https://storage.googleapis.com/mnist-soc/test.png).

Note that the function that you created in Section 5.2 will be deployed in Step 5\. If you use `savedmodel3.zip` and `test.png`, it will fail. But you will know that the function is working because you will get an error message that the model could not be loaded. This is an acceptable outcome for the model you created.

Step 5: Deploy the Spring Cloud Function to Google Functions. In this step, you take the function you created in Section 5.2 and deploy it into the Google Cloud Functions environment. The prerequisites and steps are the same as discussed in Chapter [2](#526597_1_En_2_Chapter.xhtml).

Prerequisites:

*   Google account

*   Subscription to Google Cloud Functions

*   Google CLI (This is critical, as it is a more efficient way than going through the Google Portal)

*   Code from GitHub at [`https://github.com/banup-kubeforce/DJLXRay-GCP.git`](https://github.com/banup-kubeforce/DJLXRay-GCP.git)

Modify the Spring Cloud Function to fit the Google Cloud Functions environment. See Listing [5-5](#526597_1_En_5_Chapter.xhtml#PC11).

```
org.springframework.cloud
spring-cloud-function-adapter-gcp

org.springframework.cloud
spring-cloud-dependencies
${spring-cloud.version}
pom
import

com.google.cloud
spring-cloud-gcp-dependencies
3.3.0
pom
import

org.springframework.boot
spring-boot-maven-plugin

target/deploy

org.springframework.cloud
spring-cloud-function-adapter-gcp
3.2.7

com.google.cloud.functions
function-maven-plugin
0.9.1

org.springframework.cloud.function.adapter.gcp.GcfJarLauncher
8080

Listing 5-5
Dependencies for GCP Added
```

Deploy the Spring Cloud Function to Google Cloud Functions. Make sure that you build and package before you run the following command. A JAR file must be present in the `target/deploy` directory in the root of your project.

The saved model that you are going to test with is 400MB, so you have to accommodate this by increasing the memory to 4096 and setting the timeout to 540 seconds:

```
gcloud functions deploy DJLXRay-GCP --entry-point org.springframework.cloud.function.adapter.gcp.GcfJarLauncher --runtime java11 --trigger-http --source target/deploy --memory 4096MB --timeout 540
```

Once this runs successfully, you will get the output shown in Figure [5-16](#526597_1_En_5_Chapter.xhtml#Fig16).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig16_HTML.jpg)

A screenshot of multiple programming functions. It denotes the deployed function, memory, and time.

Figure 5-16

Successfully deployed function with the specifed memory and timeout

Navigate to your Google Cloud Functions console to verify and to get the URL to test. See Figure [5-17](#526597_1_En_5_Chapter.xhtml#Fig17).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig17_HTML.jpg)

A screenshot includes functions and cloud functions in the console. It highlights the create function and refresh. It indicates a filter option with details of the first gen.

Figure 5-17

Function shows up in the console

You now test in the Cloud Function console by providing input (see Figure [5-18](#526597_1_En_5_Chapter.xhtml#Fig18)). Note that you have to increase the memory to 4096MB with a timeout set to 540s just to be safe:

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig18_HTML.png)

A screenshot highlights the cloud function, D J L X ray -G C P. The testing option with Test the function is highlighted. The test was successfully executed.

Figure 5-18

Successful execution of the test

```
{"url":"https://djl-ai.s3.amazonaws.com/resources/images/chest_xray.jpg",
"savedmodelpath":"https://storage.googleapis.com/mnist-soc/saved_model.zip"}
```

If you scroll down the test console, you get the execution times. This shows that the function execution took 16392ms, as shown in Figure [5-19](#526597_1_En_5_Chapter.xhtml#Fig19). This is 16s for execution, which is phenomenal. This is faster because you stored the saved model in Google Cloud Storage, which is closer to the function.

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig19_HTML.png)

A screenshot of the window denotes the multiple output logs. It highlights one log code no-16392, code- 200 with box .It displays the execution time.

Figure 5-19

Logs show the execution times

**This** section explored the capabilities of TensorFlow and explained how you can use DJL and Spring Cloud Function together to access a saved TensorFlow model. DJL makes it easy for Java programmers to access any of the saved models generated using Python frameworks, such as PyTorch (`pytorch.org`) and TensorFlow.

You also found that you have to set the memory and timeout based on the saved model size and store the model closer to the function, such as in Google’s storage offerings.

## 5.5 Model Serving with Spring Cloud Function with AWS Lambda and TensorFlow

This section emulates what you did in Chapter [2](#526597_1_En_2_Chapter.xhtml) for Lambda. It is best to finish that exercise before trying this one.

The prerequisites are the same as in Chapter [2](#526597_1_En_2_Chapter.xhtml). Here they are for your reference:

*   AWS account

*   AWS Lambda function subscription

*   AWS CLI (optional)

*   Code from GitHub at [`https://github.com/banup-kubeforce/DJLXRay-AWS.git`](https://github.com/banup-kubeforce/DJLXRay-AWS.git)

Step 1: Prep your Lambda environment. Ensure that you have access and a subscription to the AWS Lambda environment.

Step 2: Modify the Spring Cloud Function to fit the AWS Lambda environment. You need to add the DJL dependencies to the `pom.xml` file that you created in Chapter [2](#526597_1_En_2_Chapter.xhtml); see Listing [5-6](#526597_1_En_5_Chapter.xhtml#PC14).

```
ai.djl
api
0.12.0

ai.djl.tensorflow
tensorflow-api
0.12.0

ai.djl.tensorflow
tensorflow-engine
0.12.0

ai.djl.tensorflow
tensorflow-native-auto
2.4.1
runtime

org.projectlombok
lombok
true

Listing 5-6
DJL Dependencies
```

Step 3: Deploy the Spring Cloud Function to Lambda. You should follow the process outlined in Chapter [2](#526597_1_En_2_Chapter.xhtml) to build and package the Spring Cloud Function and deploy it to Lambda.

Step 4: Test. Once you deploy the function to Lambda, test it with Postman. You should get the result shown in Figure [5-20](#526597_1_En_5_Chapter.xhtml#Fig20).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig20_HTML.jpg)

A screenshot of the window of the successful execution. It highlights the body and pretty options and mentions the class name with probability.

Figure 5-20

Successful execution

## 5.6 Spring Cloud Function with AWS SageMaker or AI/ML

This section explores the offering from AWS called SageMaker and shows how you can use Spring Cloud Function with it.

AWS SageMaker ([`https://aws.amazon.com/sagemaker/`](https://aws.amazon.com/sagemaker/)) is a comprehensive platform for AI/ML activities. It is like a one-stop shop for creating and deploying ML models. Figure [5-21](#526597_1_En_5_Chapter.xhtml#Fig21) shows AWS SageMaker’s flow.

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig21_HTML.png)

A model of the A W S sage maker. The process includes labeling data, building, training, tuning, deploying, and discovering. Each step has a logo associated with it.

Figure 5-21

AWS SageMaker flow

SageMaker allows you to build and deploy models with Python as the language of choice, but when it comes to endpoints, there are Java SDKs much like AWS Glue that create prediction APIs or serve models for further processing. You can leverage Lambda functions for these APIs.

So, as you saw in TensorFlow, you have to work in Python and Java to model and expose models for general-purpose use.

Let’s run through a typical example and see if you can then switch to exposing APIs in Spring Cloud Function.

Note

This example uses the same sample to build, train, and deploy as in this hands-on tutorial in AWS.

[`https://aws.amazon.com/getting-started/hands-on/build-train-deploy-machine-learning-model-sagemaker/`](https://aws.amazon.com/getting-started/hands-on/build-train-deploy-machine-learning-model-sagemaker/)

Step 1: Create a notebook instance. Log on to SageMaker and create a notebook instance, as shown in Figure [5-22](#526597_1_En_5_Chapter.xhtml#Fig22). Note: It is assumed that you have walked through the tutorial that Amazon provided.

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig22_HTML.jpg)

A screenshot of the create notebook instance. It includes Notebook instance settings, Permissions, and encryption. It asks for the notebook instance name, type, elastic inference, platform identifier, and I A M role.

Figure 5-22

Notebook instance in SageMaker with properties set

Your notebook instance will be created, as shown in Figure [5-23](#526597_1_En_5_Chapter.xhtml#Fig23).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig23_HTML.png)

A screenshot of the deployment of the notebook. It highlights the amazon sage maker and search notebook instance. It asks for name, instance, creation time, status, and actions.

Figure 5-23

Successful deployment of the notebook

Step 2: Prepare the data. Use Python to prepare the data. This example uses the XGBoost ML algorithm. See Figure [5-24](#526597_1_En_5_Chapter.xhtml#Fig24).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig24_HTML.png)

A screenshot of the jupyter notebook. It highlights the option for new notebooks. It has the title files, running, cluster, sage maker example, and conda.

Figure 5-24

Pick a framework in the Jupyter notebook

As you can see from the list, most frameworks use Python. This example uses `conda_python3`, as suggested in the AWS tutorial.

Copy and paste the Python code into the Jupyter notebook cell and run it. You will get a “success” message, as shown in Figure [5-25](#526597_1_En_5_Chapter.xhtml#Fig25).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig25_HTML.jpg)

A screenshot of the window of the Jupyter notebook code. It creates a sage maker for instance. In this program, the log mentions import libraries, defines I A M role, X G boost container.

Figure 5-25

Code to create a SageMaker instance

Copy and paste the code to create the `s3` bucket to store your model, as shown in Figure [5-26](#526597_1_En_5_Chapter.xhtml#Fig26).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig26_HTML.jpg)

A screenshot of the window for the creation of a bucket. This program includes the bucket name, steps, and process. The bucket was created successfully.

Figure 5-26

Create a bucket

Now copy and paste the code to download data into a dataframe, as shown in Figure [5-27](#526597_1_En_5_Chapter.xhtml#Fig27).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig27_HTML.jpg)

A screenshot of the window for downloading the data to a data frame. The program includes the process of trying, except, and printing data.

Figure 5-27

Download the data into a dataframe

Scuffle and split the dataset, as shown in Figure [5-28](#526597_1_En_5_Chapter.xhtml#Fig28).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig28_HTML.jpg)

A screenshot of the window for the work on the dataset. The program includes splitting the dataset to train data, test data, split data, and print.

Figure 5-28

Work on the dataset

Step 3: Train the model. See Figure [5-29](#526597_1_En_5_Chapter.xhtml#Fig29).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig29_HTML.jpg)

A screenshot of the window for inputs and outputs. It indicates the training of the model with 3 steps.

Figure 5-29

Train the model

You have to wait for Step 3 to finish before deploying the model; see Figure [5-30](#526597_1_En_5_Chapter.xhtml#Fig30).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig30_HTML.jpg)

A screenshot of the window with the input and outputs. It indicates the S 3 training completion. It mentions that the training job was completed.

Figure 5-30

Training is complete

Step 4: Deploy the model. Make a note of the compute sizes used. This will impact your billing. See Figure [5-31](#526597_1_En_5_Chapter.xhtml#Fig31).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig31_HTML.jpg)

A screenshot of the window for the deployment of the model. It indicates the programming function.

Figure 5-31

Deploy the model

Step 5: Make a note of the endpoints. See Figures [5-32](#526597_1_En_5_Chapter.xhtml#Fig32) and [5-33](#526597_1_En_5_Chapter.xhtml#Fig33).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig33_HTML.png)

A screenshot of the window indicates the endpoint settings. It includes the name, type, A R N, creation time, status, last updated, U R L.

Figure 5-33

Details of the endpoint

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig32_HTML.png)

A screenshot of the window of Amazon sage maker. It has endpoints that include the name, A R N, creation time, status, and last update.

Figure 5-32

Endpoint of the deployment

Step 6: Create the Spring Cloud Function code to access the endpoint. Listing [5-7](#526597_1_En_5_Chapter.xhtml#PC15) shows the POM dependencies.

```
com.amazonaws
aws-java-sdk-sagemakerruntime
1.11.979

com.amazonaws
aws-java-sdk-core

com.amazonaws
aws-java-sdk-core
1.11.979

Listing 5-7
AWS SDK Dependencies
```

Create a `Supplier` class to call and get the result from the SageMaker endpoint. The `SupplierFunction`, unlike discussed in Section 5.3, will invoke an endpoint URL and provide the results. Here, you use SageMaker’s own model-serving capabilities. The Spring Cloud Function acts as a client for SageMaker. See Figure [5-34](#526597_1_En_5_Chapter.xhtml#Fig34).

![](images/526597_1_En_5_Chapter/526597_1_En_5_Fig34_HTML.jpg)

A screenshot of the window with the programming function input and output components. It represents the sage maker supplier in Java.

Figure 5-34

SageMakerSupplier.java

Deploy the function in Lambda, as shown in Chapter [2](#526597_1_En_2_Chapter.xhtml).

This section explained how to create and deploy a model in AWS SageMaker. You then called the SageMaker endpoint using the SageMaker JDK client in the Spring Cloud Function, which was deployed in AWS Lambda.

The Java-based Lambda function can be tuned to be more responsive and have a shorter cold startup time by using mechanisms such GraalVMs.

## 5.7 Summary

As you learned in this chapter, you can serve models using Spring Cloud Function. But you also learned that serving models using Spring Cloud Function and Java is a stretch because the AI/ML models are written in Python. While Python may be popular, it is also important to note that in an enterprise, Java is king. Finding ways to leverage Java in AI/ML is the key to having an integrated environment within your enterprise. Cold starts of Python-based functions take a long time. This is where using Java and frameworks such as GraalVM speeds up the startup times.

The next chapter explores some real-world use cases of IoT and Conversation AI and explains how Spring Cloud Function can be used.

Footnotes [1](#526597_1_En_5_Chapter.xhtml#Fn1_source)   [2](#526597_1_En_5_Chapter.xhtml#Fn2_source)  

# 6. Spring Cloud Function and IoT

This chapter covers Spring Cloud Function implementations with IoT. You'll see some real-world examples from manufacturing and logistics. You explore how Spring Cloud Function can operate with existing IoT platforms on the cloud and in datacenters. You also explore some specific implementations in Penske, which is interesting, as they can be applied to nearly all IoT-related scenarios.

Before you explore the solutions, you need to dive a bit into IoT and understand the status of the IoT market. You'll also look at some surveys on why Java is the preferred enterprise language for IoT development.

## 6.1 The State of IoT

IoT (Internet of Things) has been growing and is expected to grow 22 percent in 2022\. This phenomenal growth has led to a plethora of data that needs to be acquired and processed.

According to a study by IoT Analytics Research in September 2021, the IoT device market is expected to grow phenomenally and by 2025 it is predicted to reach 25 billion devices. More about the study is available at [`https://iotbusinessnews.com/2021/09/23/13465-number-of-connected-iot-devices-growing-9-to-12-3-billion-globally-cellular-iot-now-surpassing-2-billion/`](https://iotbusinessnews.com/2021/09/23/13465-number-of-connected-iot-devices-growing-9-to-12-3-billion-globally-cellular-iot-now-surpassing-2-billion/).

With this growth potential, you can safely assume that the supporting technologies will also grow. These technologies not only include hardware sensors and IoT gateways, but also technologies such as microservices and functions. The IoT industry is best suited to implement these technologies, as they are highly distributed and rely heavily on the software components to be small and efficient.

Serverless function-based environments that are triggered on demand are perfectly suited to IoT, as they can save significant cost. Traditional approaches to IoT relied on dedicated applications running 24/7\. They used up a lot of resources, adding to the cost of operation. With the nearly ephemeral nature of serverless functions, this cost can be moved to the “pay per use” approach.

Before you start working on some of the examples of IoT and Spring Cloud Function, it is best to understand why you need to code in Java. There are many alternative languages you can code in, but Java is the best for IoT and here is why:

*   Java was designed with IoT in mind. If you look at early Java examples from Sun Microsystems, you will see that synchronization of digital clocks was referred to often.

*   Java has been ported to many microcontrollers.

*   Java is designed to run in a resource-constrained environment such as sensors.

*   JVM makes code portable across platforms; you write once run anywhere.

As per the IoT survey done by [`iot.eclipse.org`](http://iot.eclipse.org), which is available at [`https://iot.eclipse.org/community/resources/iot-surveys/assets/iot-developer-survey-2020.pdf`](https://iot.eclipse.org/community/resources/iot-surveys/assets/iot-developer-survey-2020.pdf), you can see that Java is the preferred language, both in the edge and in the cloud. Implementations on the edge are moving to containerization, per the study. This is great for Knative on Kubernetes installations on the edge.

Furthermore, the Spring Framework has been the platform of choice for many IIoT (Industrial IoT) projects. You can review a presentation at a SpringOne event in 2017 at [`https://youtu.be/MReQD9tXQuA`](https://youtu.be/MReQD9tXQuA).

Components like Spring Cloud Streams, Spring Cloud Data Flow, Spring Integration, Spring MQTT, Spring Data, and Spring XD play a significant role in IoT data acquisition, storing, and the transformation pipeline.

### 6.1.1 Example Spring Implementation

Consider this example implementation of Spring and its components in a connected fleet scenario.

The team at Penske leveraged Spring-based microservices, Spring Cloud Streams in combination with Spring Cloud Data Flow, to acquire data from sensors that were installed on Penske trucks. This application allowed Penske to monitor the trucks, do predictive maintenance, and manage the trucks in real-time. See Figure [6-1](#526597_1_En_6_Chapter.xhtml#Fig1).

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig1_HTML.png)

A diagram depicts components of Penske&#x2019;s connected fleet. The truck fleet sends 2 billion pings per day to poller apps, which along with spring cloud data flow streams, and microservices connect to the Tanzu application service. Kafka on A W S along with V M ware Tanzu gemfire, Postgres, and Greenplum receive this data. Business users, real time analytics, and wavefront for observability as a service are the end users.

Figure 6-1

Penske connected fleet implementation with Spring Cloud

More information is available on the SpringOne website at [`https://springone.io/2020/sessions/iot-scale-event-stream-processing-for-predictive-maintenance-at-penske`](https://springone.io/2020/sessions/iot-scale-event-stream-processing-for-predictive-maintenance-at-penske).

Let’s dig a little more into the IoT process; see Figure [6-2](#526597_1_En_6_Chapter.xhtml#Fig2).

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig2_HTML.jpg)

A flow diagram depicts the I o T process flow that starts from I o T sensors, stream acquisition, raw storage, transformation, data lake storage, and ends in analysis.

Figure 6-2

IoT Process flow

As in any data pipeline process, the IoT process includes acquiring data from sensors in the field. This data is then stored, transformed, and loaded into data lakes for further analysis.

Depending on the type of application, sensors send data either through a trigger event such as a repair or collision or via regular intervals. These intervals are 30 seconds to a minute in the case of logistics tracking or every few hours in the case of maintenance data. The data usually comes in as streams that need to be acquired. The most popular mechanism for acquisition is Apache Kafka.

### 6.1.2 An Argument for Serverless Functions On-Premises

Since the sensors send data in intervals, it is best to have the input handled by a cloud function rather than dedicated microservices. Dedicated microservices need to be served up in an environment and do not have the ability to scale down to zero when invoked. They can only be provisioned in environments that need to be available when invoked. This makes microservices a costly approach. Functions, on the other hand, are only invoked when there is an event trigger. Utilization of resources goes down to zero if they are not invoked. This is because the underlying serverless infrastructure such as Lambda and Knative can be scaled down to zero when not invoked.

This makes it a more cost-effective alternative. This applies to both on-premises and on the cloud. If it is on-premises, the argument against cloud functions on a serverless environment would be that the infrastructure cost has already been accounted for. Therefore, a dedicated microservice that is always on would be feasible. Wasting resources because you have them is not a prudent way to efficiently use IT. With functions running on serverless environments, such as Knative running on OpenShift or VMware Tanzu, on-premises would help save resources that could be used to run other activities. The on-premise resources are finite, so it's prudent to make the best use of them.

## 6.2 Spring Cloud Function on the Cloud with AWS IoT

Consider a use case where an automotive assembly plant wants to ensure that its robots are performing well. The engineers want to ensure that each of the robot's data is monitored for any anomalies and, in an event of a breakdown, the engineers are alerted. The assembly plant is firewalled and any data that needs to be analyzed can be sent to the cloud.

The solution is a hybrid cloud environment that isolates the assembly plant floor but connects to the cloud to send data from the plant floor. Figure [6-3](#526597_1_En_6_Chapter.xhtml#Fig3) shows a solution that is implemented with AWS products that can be deployed on-premises and connect to the cloud to synchronize the data with the rest of the systems involved in decision-making.

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig3_HTML.png)

A flow diagram has 2 modules with 3 steps. Step 1, in the automotive assembly plant module, sensors send data to spring cloud components on-premises, A W S lambda and I o T greengrass. Step 2, In A W S cloud module I o T core receives data from the plant module through a firewall and sends it to Lambda. Step 3, data from lambda is stored in amazon's simple storage service, S 3.

Figure 6-3

Manufacturing plant process flow with AWS and Spring Cloud Function

Solution components:

*   AWS IoT Greengrass on-premises

*   Prebuilt AWS Lambda functions that capture the sensor data

*   AWS IoT core on cloud

*   Spring Cloud Function running on AWS Lambda

*   S3 storage to store the data

![](images/526597_1_En_6_Chapter/526597_1_En_6_Figa_HTML.png)

A table of 5 rows and 2 columns. It describes components and their description. Components are A W S I o T greengrass on-premises, prebuilt A W S lambda functions that capture the sensor data, A W S I o T core on a cloud, spring cloud function running on A W S lambda, and A W S S 3.

You can build the solution using AWS IoT Greengrass and leverage a Spring Cloud Function that is deployed on Lambda. The point of this exercise is to understand the capabilities of Spring Cloud Function as a component that is integral to the solution.

The AWS IoT Greengrass implementation extends AWS Cloud to on-premises. You can run Lambda functions on Greengrass’s edge.

Step 1: Install AWS IoT Greengrass. Install AWS IoT Greengrass on a device ([`https://docs.aws.amazon.com/greengrass/v1/developerguide/install-ggc.xhtml`](https://docs.aws.amazon.com/greengrass/v1/developerguide/install-ggc.xhtml)). To test it, I installed the software on Windows. I used the tutorial at [`https://aws.amazon.com/blogs/iot/aws-iot-greengrass-now-supports-the-windows-operating-system/`](https://aws.amazon.com/blogs/iot/aws-iot-greengrass-now-supports-the-windows-operating-system/) to get my first Greengrass implementation.

Once you have AWS IoT up and running, you need to create a function to connect to devices and collect data. Let’s create a sample function.

Step 2: Spring Cloud Function to publish an MQTT message. You can clone the project from GitHub at [`https://github.com/banup-kubeforce/AWSIots3-V2.git`](https://github.com/banup-kubeforce/AWSIots3-V2.git).

Devices communicate with MQTT, so you need to leverage MQTT as a protocol. Create a Spring Cloud Function that publishes an MQTT message and create a consumer function to call MQTT publish.

This consumer will publish data using the `MqttPublish` class and will be the function that is exposed by Lambda; see Listing [6-1](#526597_1_En_6_Chapter.xhtml#PC1).

```
package com.kubeforce.awsgreengrassiot;
import org.hibernate.cache.internal.StandardTimestampsCacheFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
public class MqttConsumer  implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(MqttConsumer.class);
@Autowired
private MqttPublish mqttPublish;
@Override
public void accept (Map map )
{
LOGGER.info("Adding Device info", map);
MqttPublish mqttPublish= new MqttPublish();
}
}
Listing 6-1
MqttConsumer.java
```

Next, create a `publish` class to publish messages to MQTT.

The `MqttPublish` class sends data using the `IoTDataClient` that is provided by AWS Greengrass SDK, to construct and send data. See Listing [6-2](#526597_1_En_6_Chapter.xhtml#PC2).

```
import java.nio.ByteBuffer;
import java.util.Timer;
import java.util.TimerTask;
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.greengrass.javasdk.IotDataClient;
import com.amazonaws.greengrass.javasdk.model.*;
public class MqttPublish {
static {
Timer timer = new Timer();
// Repeat publishing a message every 5 seconds
timer.scheduleAtFixedRate(new PublishDeviceInfo(), 0, 5000);
}
public String handleRequest(Object input, Context context) {
return "Here is the device info";
}
}
class PublishDeviceInfo extends TimerTask {
private IotDataClient iotDataClient = new IotDataClient();
private String publishMessage = String.format("Device info sent from device running on platform: %s-%s using Java", System.getProperty("os.name"), System.getProperty("os.version"));
private PublishRequest publishRequest = new PublishRequest()
.withTopic("device/info")
.withPayload(ByteBuffer.wrap(String.format("{\"message\":\"%s\"}", publishMessage).getBytes()))
.withQueueFullPolicy(QueueFullPolicy.AllOrException);
public void run() {
try {
iotDataClient.publish(publishRequest);
} catch (Exception ex) {
System.err.println(ex);
}
}
}
Listing 6-2
MqttPublish.java
```

Step 3: Deploy the Spring Cloud Function on AWS Greengrass locally.

AWS provides a great guide for deploying Lambda function on Greengrass at [`Run Lambda functions on the AWS IoT Greengrass core - AWS IoT Greengrass (amazon.com)`](https://docs.aws.amazon.com/greengrass/v1/developerguide/lambda-functions.xhtml).

As described in Chapter [2](#526597_1_En_2_Chapter.xhtml), you will bundle the Spring Cloud Function and publish it to Lambda. Alternatively, you can also use these CLI commands:

```
aws lambda create-function \
--region aws-region \
--function-name  MqttConsumer \
--handler executable-name \
--role role-arn \
--zip-file fileb://Application_Name.zip \
--runtime arn:aws:greengrass:::runtime/function/executable
```

Navigate to the AWS IoT management console, and then click Greengrass, followed by Deployments. You can see the deployed components. Figure [6-4](#526597_1_En_6_Chapter.xhtml#Fig4) shows a sample of the management console.

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig4_HTML.jpg)

A screenshot of the A W S I o T management console. Monitor tab on the left is selected. It has I o T metrics and job execution metrics options. I o T metrics has 4 graphs with headings that include successful connections and protocol rates. Other main tabs on the left are connect, test, and manage.

Figure 6-4

AWS IoT Management console with some successful connections

Step 4: Create a Spring Cloud Function to get data from the IoT core. Create a class to subscribe and get messages from Mqtt.

If you want to access the data that you published, you can run `MqttSubscriber.java` from the command line. You can use `MqttSubscriber.java`. This subscribes to a specific topic, `device/info` for example, and gets the messages. See Listing [6-3](#526597_1_En_6_Chapter.xhtml#PC4).

```
import software.amazon.awssdk.crt.CRT;
import software.amazon.awssdk.crt.CrtRuntimeException;
import software.amazon.awssdk.crt.mqtt.MqttClientConnection;
import software.amazon.awssdk.crt.mqtt.MqttClientConnectionEvents;
import software.amazon.awssdk.crt.mqtt.QualityOfService;
import software.amazon.awssdk.iot.iotjobs.model.RejectedError;
import java.nio.charset.StandardCharsets;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.atomic.AtomicReference;
public class MqttSubscriber  {
static String ciPropValue = System.getProperty("aws.crt.ci");
static boolean isCI = ciPropValue != null && Boolean.valueOf(ciPropValue);
static String topic = "device/info";
static String message = "Device Info";
static int    messagesToPublish = 10;
static CommandLineUtils cmdUtils;
static void onRejectedError(RejectedError error) {
System.out.println("Request rejected: " + error.code.toString() + ": " + error.message);
}
/*
* When called during a CI run, throw an exception that will escape and fail the exec:java task
* When called otherwise, print what went wrong (if anything) and just continue (return from main)
*/
static void onApplicationFailure(Throwable cause) {
if (isCI) {
throw new RuntimeException("BasicPubSub execution failure", cause);
} else if (cause != null) {
System.out.println("Exception encountered: " + cause.toString());
}
}
public String MqttSubscribe(){
final String[] payload = {""};
cmdUtils = new CommandLineUtils();
cmdUtils.registerProgramName("PubSub");
cmdUtils.addCommonMQTTCommands();
cmdUtils.addCommonTopicMessageCommands();
cmdUtils.registerCommand("key", "", "Path to your key in PEM format.");
cmdUtils.registerCommand("cert", "", "Path to your client certificate in PEM format.");
cmdUtils.registerCommand("client_id", "", "Client id to use (optional, default='test-*').");
cmdUtils.registerCommand("port", "", "Port to connect to on the endpoint (optional, default='8883').");
cmdUtils.registerCommand("count", "", "Number of messages to publish (optional, default='10').");
topic = cmdUtils.getCommandOrDefault("topic", topic);
message = cmdUtils.getCommandOrDefault("message", message);
messagesToPublish = Integer.parseInt(cmdUtils.getCommandOrDefault("count", String.valueOf(messagesToPublish)));
MqttClientConnectionEvents callbacks = new MqttClientConnectionEvents() {
@Override
public void onConnectionInterrupted(int errorCode) {
if (errorCode != 0) {
System.out.println("Connection interrupted: " + errorCode + ": " + CRT.awsErrorString(errorCode));
}
}
@Override
public void onConnectionResumed(boolean sessionPresent) {
System.out.println("Connection resumed: " + (sessionPresent ? "existing session" : "clean session"));
}
};
try {
MqttClientConnection connection = cmdUtils.buildMQTTConnection(callbacks);
if (connection == null)
{
onApplicationFailure(new RuntimeException("MQTT connection creation failed!"));
}
CompletableFuture connected = connection.connect();
try {
boolean sessionPresent = connected.get();
System.out.println("Connected to " + (!sessionPresent ? "new" : "existing") + " session!");
} catch (Exception ex) {
throw new RuntimeException("Exception occurred during connect", ex);
}
CountDownLatch countDownLatch = new CountDownLatch(messagesToPublish);
CompletableFuture subscribed = connection.subscribe(topic, QualityOfService.AT_LEAST_ONCE, (message) -> {
payload[0] = new String(message.getPayload(), StandardCharsets.UTF_8);
System.out.println("MESSAGE: " + payload[0]);
countDownLatch.countDown();
});
subscribed.get();
countDownLatch.await();
CompletableFuture disconnected = connection.disconnect();
disconnected.get();
// Close the connection now that we are completely done with it.
connection.close();
} catch (CrtRuntimeException | InterruptedException | ExecutionException ex) {
onApplicationFailure(ex);
}
return payload[0];
}
}
Listing 6-3
MqttSubscriber.java with Command-Line Utilities to Publish to AWS IoT
```

Create a class to upload the message to an S3 bucket. You can use the `S3Upload.java` provided; see Listing [6-4](#526597_1_En_6_Chapter.xhtml#PC5).

```
package com.kubeforce.awsiots3;
import software.amazon.awssdk.core.sync.RequestBody;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.model.PutObjectRequest;
import software.amazon.awssdk.services.s3.S3Client;
public class S3Upload {
public String S3upload(String payload) {
//set-up the client
Region region = Region.US_WEST_2;
S3Client s3 = S3Client.builder().region(region).build();
String bucketName = "greengrass";
String key = "IoT";
s3.putObject(PutObjectRequest.builder().bucket(bucketName).key(key)
.build(),
RequestBody.fromString(payload));
s3.close();
return ("success");
}
}
Listing 6-4
S3upload.java
```

Finally, create a Spring Cloud Function called `Consumer` that calls the `MqttSubscriber` and `S3Upload` classes. See Listing [6-5](#526597_1_En_6_Chapter.xhtml#PC6).

```
import java.util.Map;
import java.util.function.Consumer;
public class IoTConsumer implements Consumer> {
@Override
public void accept (Map map)
{
MqttSubscriber mqttSubscriber = new MqttSubscriber();
S3Upload s3Upload = new S3Upload();
s3Upload.S3upload(mqttSubscriber.MqttSubscribe());
}
}
Listing 6-5
IoTConsumer.java
```

This function can be deployed as a Lambda function, as shown in Chapter [2](#526597_1_En_2_Chapter.xhtml).

You can find a sample execution and the outcomes on GitHub at [`https://github.com/banup-kubeforce/AWSIots3-V2.git`](https://github.com/banup-kubeforce/AWSIots3-V2.git).

In this section, you were able to deploy AWS IoT Greengrass locally, deploy a local Lambda function with Spring Cloud function code, publish data to the cloud, and store the data into `S3` with another Spring Cloud function.

This is straightforward, as AWS has Java-based SDKs to help build the Spring Cloud Function clients. You also learned that you can deploy Lambda functions locally.

## 6.3 Spring Cloud Function on the Cloud with Azure IoT

This is the same use case as in the prior example, where an automotive assembly plant wants to ensure that its robots are performing well. The data from the sensors needs to be collected and analyzed for anomalies. Each assembly plant is walled off from the public Internet through firewalls.

The solution with Azure is very similar to the one you saw with AWS Greengrass. You need a way to get the device information from the plant floor, analyze it, and respond to it with an action within the plant floor, and then send the data to the cloud for further processing. Actionable insights are made closer to the devices.

Components:

*   Azure IoT Edge Device

*   Azure IoT hub

*   Spring Cloud Function running on Azure Functions environment and deployed on the Azure IoT Edge

*   Azure Blob store to store data in the cloud

### 6.3.1 Azure IoT Edge Device

The IoT Edge device manages communications between downstream leaf devices and the IoT hub. In this case, the leaf devices are robots with sensors. The IoT Edge device has a runtime that enables cloud logic on the devices. It supports Azure Functions to run on the edge device. See Figure [6-5](#526597_1_En_6_Chapter.xhtml#Fig5).

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig5_HTML.jpg)

A diagram of azure I o T edge device has azure I o T edge runtime that connects to 3 modules telemetry, insight, and action. Telemetry includes temperature and pressure. Insight includes azure streaming analytics and azure M L. Action includes spring cloud function and azure functions.

Figure 6-5

Azure IoT Edge Device implementation

### 6.3.2 Azure IoT Hub

This hub is a managed service offering from Azure that allows you to collect data from IoT devices and send them to other Azure services for further processing. This is an IoT gateway where you can connect and manage your devices through a single portal. More information can be found at [`https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/iot`](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/iot).

## 6.4 Spring Cloud Function on Azure IoT Edge

Spring Cloud Function will be deployed on the Azure IoT Edge devices. Azure IoT Edge allows for deployment of containerized functions. More information can be found `at` [`https://learn.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function?view=iotedge-1.4`](https://learn.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function%253Fview%253Diotedge-1.4).

Figure [6-6](#526597_1_En_6_Chapter.xhtml#Fig6) shows this solution.

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig6_HTML.png)

A flow diagram has 2 modules. In the automotive assembly plant module, sensors send data to azure I o T edge device by T C P I P. The data is then sent via the plant firewall, to the azure cloud module, which has components, azure I o T hub, spring cloud function azure functions environment, and azure blob store.

Figure 6-6

Manufacturing plant processing using Azure and Spring Cloud Function

Let’s implement this solution.

Prerequisites:

*   VS Studio Code configured for Spring Boot, as this has a very good integration with Azure IoT

*   Azure Container Registry to store your containerized Spring Cloud Function

*   Code from GitHub at [`https://github.com/banup-kubeforce/AzureIoTSimulator.git`](https://github.com/banup-kubeforce/AzureIoTSimulator.git)

*   Azure IoTEdge and Azure IoTHub set up and configured

Step 1: Install an Azure IoT Edge device. You can follow the instructions at [`https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux?view=iotedge-2020-11`](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux%253Fview%253Diotedge-2020-11) to enable either a Windows or Linux device.

Step 2: Connect the device to Azure IoT. Since you cannot deploy the Azure Stack hub, it is best to use an Azure IoT hub on the web.

You can enable it on the Azure Portal. More information on how to configure your edge devices to connect to the IoT hub is available at [`https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux?view=iotedge-2020-11`](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux%253Fview%253Diotedge-2020-11).

You also have to register your device on the hub. See Figure [6-7](#526597_1_En_6_Chapter.xhtml#Fig7).

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig7_HTML.jpg)

A screenshot of a Microsoft azure portal. The I o T hub subscription page is selected. The management tab has headings, scale tier and units, and role-based access control with options under them. Other tabs on top are basics, networking, tags, and review plus create.

Figure 6-7

IoT hub subscription in Azure Portal

Here is an example command line:

```
az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
```

Step 3: Create a Spring Cloud Function and deploy it on Azure IoT Edge.

This code is available at [`https://github.com/banup-kubeforce/AzureIoTSimulator.git`](https://github.com/banup-kubeforce/AzureIoTSimulator.git).

You can create a Spring Cloud Function that sends information from the edge device to the IoT hub. Make sure you have the connection string that you created in Step 1.

Dependencies:

```
com.microsoft.azure.sdk.iot
iot-device-client
2.1.1

```

Write the code to connect to the IoT hub. The `iot.connection.string` variable is where you store the connection string you got in Step 1.

```
import com.microsoft.azure.sdk.iot.device.DeviceClient;
import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.net.URISyntaxException;
@Configuration
public class IOTConfiguration {
@Bean
public DeviceClient deviceClient(@Value("${iot.connection.string}") String connString) throws URISyntaxException {
return new DeviceClient(connString, IotHubClientProtocol.HTTPS);
}
}
```

Create a payload entity to construct the message:

```
package com.kubeforce.azureiotsimulator;
import java.time.LocalDateTime;
public class PayloadEntity {
private final LocalDateTime timestamp;
private final String message;
public PayloadEntity(LocalDateTime timestamp, String message) {
this.timestamp = timestamp;
this.message = message;
}
public LocalDateTime getTimestamp() {
return timestamp;
}
public String getMessage() {
return message;
}
}
```

Create the function to send the message:

```
package com.kubeforce.azureiotsimulator;
import com.microsoft.azure.sdk.iot.device.DeviceClient;
import com.microsoft.azure.sdk.iot.device.Message;
import com.microsoft.azure.sdk.iot.device.MessageSentCallback;
import com.microsoft.azure.sdk.iot.device.exceptions.IotHubClientException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.lang.invoke.MethodHandles;
import java.util.function.Function;
public class SendtoIoTFunction implements Function {
public static final Logger LOGGER = LoggerFactory.getLogger(MethodHandles.lookup().lookupClass());
@Autowired
private DeviceClient deviceClient;
Message message=null;
@Override
public String apply(PayloadEntity payloadEntity) {
try {
deviceClient.open(false);
deviceClient.sendEventAsync(message, new MessageSentCallback() {
@Override
public void onMessageSent(Message message, IotHubClientException e, Object o) {
LOGGER.info("IOT Response || STATUS CODE || {}",message.toString());
}
}, null);
deviceClient.close();
} catch (IotHubClientException e) {
throw new RuntimeException(e);
}
return null;
}
}
```

Step 4: Deploy the function into Azure Function on the edge devices.

You will have to containerize the function as discussed in Chapter [2](#526597_1_En_2_Chapter.xhtml). Instead of pushing to Dockerhub, you have to push it to Azure Container Registry. Information on Azure Container Registry is available at [`https://azure.microsoft.com/en-us/products/container-registry/`](https://azure.microsoft.com/en-us/products/container-registry/).

Use the VS Studio Code features. Additional information is provided on GitHub:

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig8_HTML.jpg)

A flow diagram. The development machine stage connects to the azure cloud function, which in turn deploys to the I o T edge device function. The azure I o T edge runtime uses the sensor and function and sends insights back to the azure cloud I o T hub.

Figure 6-8

Azure function deployment on the IoT edge devices using Azure IoT Edge runtime Source: [*https://learn.microsoft.com/en-us/azure/iot-edge/media/tutorial-deploy-function/functions-architecture.png?view=iotedge-1.4*](https://learn.microsoft.com/en-us/azure/iot-edge/media/tutorial-deploy-function/functions-architecture.png%253Fview%253Diotedge-1.4)

1.  Create a deployment for a single device.

2.  Select `deployment.amd64.jso` in the `config` folder. Click Select Edge Deployment Manifest.

3.  Expand the modules to see a list of deployed and running modules. See Figure [6-8](#526597_1_En_6_Chapter.xhtml#Fig8).

This section showed that you can take the same use case of the manufacturing assembly plant and sensors and apply an Azure IoT solution to it.

You created a Spring Cloud Function and deployed it to Azure IoTEdge using the Azure Function. Detailed information is provided on GitHub at [`https://github.com/banup-kubeforce/AzureIoTSimulator.git`](https://github.com/banup-kubeforce/AzureIoTSimulator.git).

You also learned that you can use the Azure IoT Edge devices with Azure IoT Edge runtime to collect data from sensors. This is very similar to how it is done in AWS.

## 6.5 Spring Cloud Function On-Premises with IoT Gateway on a SaaS Provider (SmartSense)

Enterprises that do not want to use any of the cloud provider’s IoT components can offload the collection of sensor data to third-party companies. These third-party companies can then send the data as streams or a batch it to systems that are hosted within the enterprise's datacenter.

Figure [6-9](#526597_1_En_6_Chapter.xhtml#Fig9) shows the implementation without using a hyperscaler like Azure or AWS.

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig9_HTML.png)

An illustrated diagram. The fleet management sends G P S and vehicle Information via a cell tower to a third-party cloud I o T gateway. The gateway triggers a spring cloud function to send data to the Kubernetes platform, which is either on OpenShift or V M ware Tanzu.

Figure 6-9

Fleet management example

For this use case, a company wants to manage its fleet of vehicles and provide data to its consumers about the vehicle's health, location, maintenance, repair info, driver info, and so on, to enable visibility of its fleet and cargo. The vehicle data needs to be collected and processed in its own datacenter. See Table [6-1](#526597_1_En_6_Chapter.xhtml#Tab1).

Table 6-1

Components Description

| -![](images/526597_1_En_6_Chapter/526597_1_En_6_Figb_HTML.gif)A table with 5 rows and 2 columns. The column headers are components and descriptions. Components are the third-party I o T gateway providers, Kubernetes platform on-premises, spring cloud data flow, spring cloud functions on knative, and S 3 bucket hosted in the data center. |

Solution:

*   Third-party IoT gateway providers such as Michelin, Erickson, Samsara, and so on

*   Kubernetes platform on-premises such as OpenShift or VMware Tanzu

*   Spring Cloud Data Flow

*   S3 bucket hosted in the datacenter, like Minio or Ceph

*   Spring Cloud Function on Knative

Step 1: Contact your third-party provider to get the information about their IoT hub. Since the work of acquiring the sensor data is offloaded to third-party providers, you can assume that data will be accumulated and routed to the third-party cloud from the vehicles.

Once the data is accumulated at the third-party gateway, it gets routed to the company's datacenter via an invocation of a function.

Step 2: Set up Spring Cloud Data Flow in your environment.

*   Install Spring Cloud Data Flow in a Kubernetes environment like OpenShift or VMware Tanzu

*   Create a Spring Cloud Data Flow data pipeline.

Installation of Spring Cloud Data Flow is discussed in Chapter [4](#526597_1_En_4_Chapter.xhtml).

![](images/526597_1_En_6_Chapter/526597_1_En_6_Fig10_HTML.png)

A screenshot of a spring cloud data flow window. The streams tab is selected. Create stream is the page header. A search option is next to a data pipeline with blocks rabbit, transform, and s 3\. The cancel and create stream buttons are below.

Figure 6-10

Spring Cloud Data Flow

This example uses RabbitMQ as the source and S3 Minio/Ceph as the sink.

Step 3: Publish to RabbitMQ. See Chapter [4](#526597_1_En_4_Chapter.xhtml) for details of publishing to RabbitMQ.

Step 4: Deploy the Spring Cloud Function on Knative, as you did in Chapter [2](#526597_1_En_2_Chapter.xhtml), and expose a public endpoint for the IoT gateway to connect.

This completes the implementation of an on-premises solution that uses a third-party IoT gateway. You can leverage the serverless environment, just like AWS Lambda or Azure Functions. Knative on Kubernetes provides that serverless environment to run the Spring Cloud Function.

You leveraged Spring Cloud Data Flow as the data pipeline to collect and process the sensor data.

More information on the project is available at [`https://github.com/banup-kubeforce/onpremIoT.git`](https://github.com/banup-kubeforce/onpremIoT.git).

## 6.6 Summary

As you learned in this chapter, you can build IoT-based solutions using Spring Cloud Function, both in an on-premises environment, and as well as in AWS and Azure.

Spring Cloud Function is one of the most versatile frameworks in the Java world. It can be deployed on proprietary cloud-based serverless environments and on Knative, making it a very portable component.

Whether it is a manufacturing plant walled off from the public Internet or on the road with fleet management, you can build secure and reliable solutions with Spring Cloud Function.

# 7. Industry-Specific Examples with Spring Cloud Function

This chapter explores some of the industry-specific implementations of Spring Cloud Function.

It leverages the IBM Cloud offering to demonstrate that Spring Cloud Function is supported on any cloud offering.

Some of the examples in this chapter are real-world scenarios in which functions play a critical role. For example, a function that sends alarms about pipeline leaks and a chat function that helps customers solve problems.

## 7.1 Oil/Natural Gas Pipeline Tracking with Spring Cloud Function and IOT

This section looks at how Spring Cloud Function can be used in the context of natural gas pipelines.

Miles and miles of gas pipelines need to be constantly monitored for leaks, cracks, temperature fluctuations, and pressure issues in order to avoid costly breakdowns in the transmission of gas and to avoid explosions.

This solution leverages IBM Cloud and its IoT platform offerings along with smart gateways to capture sensor data and provide analysis and visualizations. You'll see how to deploy Spring Cloud Function and integrate it with IBM Event streams to store the streaming data into IBM Cloudant DB. See Figure [7-1](#526597_1_En_7_Chapter.xhtml#Fig1).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig1_HTML.png)

A schema of implementation of gas pipeline. It includes a Gas pipeline, sensors, I o T gateway, cellular network, M Q T T, and a monitoring dashboard.

Figure 7-1

Gas pipeline IoT implementation

The required components are outlined in Table [7-1](#526597_1_En_7_Chapter.xhtml#Tab1).

Table 7-1

Components for an Example Solution

| Sensor | The Preferred Sensor |
| --- | --- |
| IoT Gateway | Your preferred gateway product |
| IBM Cloud Functions | [`https://cloud.ibm.com/functions/`](https://cloud.ibm.com/functions/) |
| IBM Watson IoT Platform | [`https://www.ibm.com/cloud/watson-iot-platform`](https://www.ibm.com/cloud/watson-iot-platform) |
| IBM Watson IoT Platform – Message Gateway | [`https://www.ibm.com/docs/en/wip-mg`](https://www.ibm.com/docs/en/wip-mg) |
| IBM Event Streams | [`https://www.ibm.com/cloud/event-streams`](https://www.ibm.com/cloud/event-streams) |
| IBM Cloudant DB | [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant) |

### 7.1.1 Sensors

There are multiple parameters that need to be measured and tracked when monitoring the health of a pipeline. This data can be categorized into asset and external data.

Asset data can include pressure, flow rate, wall thickness, and cracks. External data can include temperature, humidity, pH levels, and soil resistivity. Smart sensors can be installed along the length of the pipeline to transmit information to a nearby IoT gateway.

### 7.1.2 IoT Gateway

These devices act as sensor data aggregators and send the acquired data to receiving systems like the Watson IoT platform. They also allow the Watson IoT platform to connect and manage sensor devices. There are many cloud-ready gateways in the market.

These cloud-ready gateways allow for connectivity to different cloud platforms, including Azure, AWS, the Watson IoT platform, and so on.

### 7.1.3 IBM Cloud Functions

IBM Cloud offers serverless cloud functions that can be either scripted on the web-based console or deployed on Docker containers. If you are developing in Java and dotnet, you need to use the Docker option. Alternatively, Knative on Kubernetes is offered as a way to deploy Spring Cloud Function with Docker images. More information is available at [`https://www.ibm.com/cloud/functions`](https://www.ibm.com/cloud/functions).

### 7.1.4 IBM Watson IoT Platform

This product from IBM also allows you to set up and manage connected devices. It has a dashboard that offers device management, MQTT support, data lifecycle management, and secure communication with TLS for MQTT. More information is available at [`https://www.ibm.com/cloud/internet-of-things`](https://www.ibm.com/cloud/internet-of-things).

### 7.1.5 IBM Watson IoT Platform: Message Gateway

IBM Message Gateway receives the message that IoT sensors send and sends it to platforms such as IBM Event Streams.

More information is available at [`https://www.ibm.com/docs/en/wip-mg`](https://www.ibm.com/docs/en/wip-mg).

### 7.1.6 IBM Event Streams

This is based on Apache Kafka and allows for managing streaming event data coming from the IBM Watson IoT platform. More information is available at [`https://www.ibm.com/cloud/event-streams`](https://www.ibm.com/cloud/event-streams).

### 7.1.7 IBM Cloudant DB

This is a fully managed distributed database that allows for storing data from the IBM Watson IoT platform. You can find more information at [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant).

Now you'll see how to realize the flow shown in Figure [7-2](#526597_1_En_7_Chapter.xhtml#Fig2), where the IoT sensors send information to the IBM Event Stream through the message gateway. The message is then received from IBM event streams through a Spring Cloud Function and stored in Cloudant DB. It is suggested you explore the integration between sensors and the message gateway at [`https://www.ibm.com/docs/en/wip-mg/5.0.0.1?topic=os-imaserver-rpm`](https://www.ibm.com/docs/en/wip-mg/5.0.0.1%253Ftopic%253Dos-imaserver-rpm).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig2_HTML.png)

A flow model of data sensor. The sensor sends information to the I B M cloud via M Q T T, event streams, and the spring cloud function. Finally, it is stored in a database.

Figure 7-2

Use case for sensor data consumption flow

Figure [7-2](#526597_1_En_7_Chapter.xhtml#Fig2) shows the flow that this example plans to achieve.

The focus is on creating a Spring Cloud Function that receives the message published to a topic called "Sensor-topic” in event streams and stores it into the Cloudant DB.

Step 1: Register with IBM Cloud and subscribe to Watson IoT platform.

You can start at [`https://www.ibm.com/cloud/internet-of-things`](https://www.ibm.com/cloud/internet-of-things) and click Get Started to start the process.

If you are just this trying out, you can use the free tier (lite). See Figure [7-3](#526597_1_En_7_Chapter.xhtml#Fig3).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig3_HTML.png)

A screenshot of the window titled I B M cloud Watson. It consists of the I o T platform application with a Create option. It has to select a location, select a pricing plan, and configure your resource.

Figure 7-3

IBM Cloud Watson IOT

Step 2: Add devices that need to be monitored.

To get to the Add Device page, click your name on the top right and click the IoT resource that was created in Step 1; see Figure [7-4](#526597_1_En_7_Chapter.xhtml#Fig4).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig4_HTML.png)

A screenshot of the window titled I B M Watson I o T platform. It has an add device page with Identify, device type and device I D options.

Figure 7-4

Add Device page

The Add Device page takes you to a step-by-step process to configure your device. You have to select your device type and device ID, and then provide the device information such as serial number, model, and so on. Then you can enter an optional token. Preview all the information you entered by clicking the Finish button. This will create the device, as shown in the Figure [7-5](#526597_1_En_7_Chapter.xhtml#Fig5).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig5_HTML.png)

A screenshot of the Watson I o T platform window. It has a browse device application to add devices and applications consisting of device ID, status, device type, class ID, address, and locations.

Figure 7-5

Add devices on the Watson IoT platform

Step 3: Subscribe to the IBM event streams.

Here again, you have the option of using a free tier (Lite). See Figure [7-6](#526597_1_En_7_Chapter.xhtml#Fig6).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig6_HTML.png)

A screenshot of I B M cloud window with event streams. It has to Create, a platform, public cloud, satellite, location, ad select pricing plan options.

Figure 7-6

IBM Cloud event streams

You then stipulate where you want to run the event stream—on a public cloud or satellite, as well as the cloud instance location. Then select your pricing plan (the Lite option is free), give it a name, and assign a resource group. You can also provide some identification tags. This will create the event stream.

Step 4: Create a topic with the required parameters.

Within your event streams dashboard, click the Create Topic button and enter the name of the topic, as well as any partitions and message retention parameters. This will create a topic, which you can see on the Event Streams page, as shown in Figure [7-7](#526597_1_En_7_Chapter.xhtml#Fig7).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig7_HTML.png)

A screenshot of the event stream dashboard. It selects the topics with options of name, partitions, retention time, retention size, and cleanup policy.

Figure 7-7

Create event stream

Step 5: Deploy a function to IBM Cloud.

This step does not use the IBM Cloud functions that come out of the box. You can use the IBM Kubernetes services to build and deploy your Spring Cloud Function on a Knative service. You can get to the IBM Kubernetes service by searching the IBM Cloud or catalog or by going directly to [`https://cloud.ibm.com/kubernetes/catalog/create`](https://cloud.ibm.com/kubernetes/catalog/create).

Now create a bare minimum cluster that is free. This is what I like about the IBM Cloud; you are really getting a free service even for a Kubernetes cluster. This is unlike other cloud providers, where you must pay for the underlying compute and storage.

If you are creating a free cluster, be aware that this cluster will be deleted after 30 days.

Once on the Kubernetes cluster page, pick the pricing plan and a Kubernetes cluster will be created for you. With Lite, you do not have the option to name the cluster. You can subscribe to the standard tier to get more control. See Figure [7-8](#526597_1_En_7_Chapter.xhtml#Fig8).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig8_HTML.png)

A screenshot of the I B M Kubernetes cluster dashboard. It has options of create, plan details, Kubernetes version, and resource details.

Figure 7-8

Subscribe to IBM Kubernetes

IBM Cloud will go through the process of creating the cluster and bring you to the page shown in Figure [7-9](#526597_1_En_7_Chapter.xhtml#Fig9).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig10_HTML.jpg)

A set of instructions after creating the cluster. It has 3 steps to connect to the cluster which include login, setting the context, and verifying. At the bottom, it has a tip.

Figure 7-10

Some boilerplate instructions after creating the cluster

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig9_HTML.png)

A screenshot of the I B M cluster dashboard. Overview with the details, node health is included.

Figure 7-9

Successful creation of the cluster

Connect to the cluster by running the following command to get the cluster information:

```
$ ibmcloud ks cluster config --cluster cc57gphf0fcppc4gtp6g
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig11_HTML.png)

A screenshot image of running the command to connect to the cluster.

Figure 7-11

Your kubectl is configured to connect to the cluster

Verify the current configuration. Run the following command to set the context of Kubectl to the Kubernetes cluster:

```
$ kubectl config current-context
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig12_HTML.jpg)

A screenshot of a command to be run to set the context to the Kubernetes cluster.

Figure 7-12

Establish the connection to your cluster

Now install Knative serving. Run the following command to install the Knative serving components:

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-crds.yaml
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig13_HTML.png)

A screenshot to run the command to install Knative c r d s. 12 configurations appeared in the console.

Figure 7-13

Knative crds are created

Run the following command to install the Knative serving core:

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-core.yaml
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig14_HTML.png)

A screenshot to run the command for installing the Knative serving core.

Figure 7-14

Knative serving core is deployed

Install Istio. Istio creates an ingress for the cluster. Run the following code to install it:

```
$ kubectl apply -l knative.dev/crd-install=true -f https://github.com/knative/net-istio/releases/download/knative-v1.7.0/istio.yaml
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig15_HTML.jpg)

A screenshot to run the command for Istio installation. It has 2 highlighted texts among multiple components.

Figure 7-15

Istio is successfully deployed

Now run the following command to configure the network components:

```
$ kubectl apply -f https://github.com/knative/net-istio/releases/download/knative-v1.7.0/net-istio.yaml
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig16_HTML.png)

A screenshot to run the command to construct network components.

Figure 7-16

Network components of Istio are created

Now install Magic DNS. It allows you to call the service from an FQDN rather than an IP address.

```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.7.1/serving-default-domain.yaml
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig17_HTML.png)

A screenshot of running the command to install Magic D N S.

Figure 7-17

Default domain (Magic DNS) created

Run the following command to get the status of the services:

```
$ kubectl get pods --namespace knative-serving
```

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig18_HTML.jpg)

A screenshot of the running of Knative components. It has the name, ready, restarts, and age as column labels.

Figure 7-18

Knative components are running

Once the status is `Running`, you can deploy Spring Cloud Function.

Step 6: Create a Cloudant database. Navigate to your subscription on the IBM Cloud and click Create Resource. Search for Cloudant.

You can also get to the Cloudant page at [`https://cloud.ibm.com/catalog/services/cloudant`](https://cloud.ibm.com/catalog/services/cloudant).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig19_HTML.jpg)

A screenshot of the window titled I B M cloud. It displays a resource list of the name, group, location, product, status, and tags as column labels.

Figure 7-19

IBM Cloud resources

Choose the Cloudant instance listed in Services and Software to start configuring your instance. Set up the Cloudant configuration as per your specification. See Figure [7-20](#526597_1_En_7_Chapter.xhtml#Fig20).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig20_HTML.jpg)

A screenshot of the I B M cloud's Cloudant-p 5\. It includes the overview, capacity, and docs as column labels. The overview includes deployment details.

Figure 7-20

IBM Cloud Cloudant specs

You can use Create Database on the top of the screen. Provide the name and partition information to create the database. Once this process is complete, you can see the database, as shown in Figure [7-21](#526597_1_En_7_Chapter.xhtml#Fig21).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig21_HTML.png)

A screenshot of the I B M cloud. It displays the Cloudant database with database creation options and the format of the database indicated by an arrow.

Figure 7-21

Cloudant DB

Step 7: Create a Spring Cloud Function to connect to the event streams and Cloudant DB. You can download it at [`https://github.com/banup-kubeforce/IBMIoTV2`](https://github.com/banup-kubeforce/IBMIoTV2).

You use the Spring Kafka and Cloudant for the Maven dependency, as shown in Listing [7-1](#526597_1_En_7_Chapter.xhtml#PC9).

```

com.ibm.cloud
cloudant
0.3.0

com.cloudant
cloudant-client
2.20.1

Listing 7-1
Dependencies
```

Listing [7-2](#526597_1_En_7_Chapter.xhtml#PC10) shows the `application.properties`.

```
spring.cloud.function.definition=iotCloudantSupplier
cloudant.db=sensordb
cloudant.url="https://apikey-v2-w2z4fgix9dlpw626eihi4g4n9w20ntyekk7jknbyr1o:1d012fa20433d315b899a2ed90f3fefb@23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix.cloudantnosqldb.appdomain.cloud"
cloudant.apikey="Service credentials-1"
spring.datasource.url=jdbc:h2:mem:employee
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
#EventStreams
#Connection
spring.kafka.jaas.enabled=true
spring.kafka.jaas.login-module=org.apache.kafka.common.security.plain.PlainLoginModule
spring.kafka.jaas.options.username=token
spring.kafka.jaas.options.password=NhZ7i_IHpf3piG99jQpIMGtZTT3tRggmfj7UhaztdNFx
spring.kafka.bootstrap-servers=broker-2-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-1-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-0-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-5-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-3-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093,broker-4-2068cxqswxtbl1kv.kafka.svc09.us-south.eventstreams.cloud.ibm.com:9093
spring.kafka.properties.security.protocol=SASL_SSL
spring.kafka.properties.sasl.mechanism=PLAIN
#Producer
spring.kafka.template.default-topic=sensor-topic
spring.kafka.producer.client-id=event-streams-kafka
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
#Consumer
listener.topic=sensor-topic
spring.kafka.consumer.group-id=sensor-topic
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
Listing 7-2
Cloudant and IBM Event Streams Configuration
```

You have to provide the device ID, protocol, data, and status of the device into a JSON object for Cloudant to store. See Listings [7-3](#526597_1_En_7_Chapter.xhtml#PC11) through [7-5](#526597_1_En_7_Chapter.xhtml#PC13).

```
package com.kubeforce.ibmiotv2;
import org.json.JSONObject;
import org.springframework.kafka.core.KafkaTemplate;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.function.Supplier;
public class EventStreamsSupplier implements Supplier {
CloudantConnector cloudantConnector = new CloudantConnector();
private KafkaTemplate template;
private List messages = new CopyOnWriteArrayList();
@Override
public String get() {
String result = messages.toString();
JSONObject iotdata = new JSONObject();
iotdata.put("message",result);
cloudantConnector.db.save(iotdata);
messages.clear();
return result;
}
}
Listing 7-5
EventStreamsSupplier.java (See GitHub for the Latest)
```

```
import com.cloudant.client.api.ClientBuilder;
import com.cloudant.client.api.CloudantClient;
import com.cloudant.client.api.Database;
public class CloudantConnector {
CloudantClient client = ClientBuilder.account("23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix")
.iamApiKey("B2DtIBIPIP1qhFt6swrl-0nbQZcY6ZB1SVti_9zKX832")
.build();
Database db = client.database("sensordb",false);
}
Listing 7-4
CloudantConnector.java
```

```
import com.cloudant.client.api.ClientBuilder;
import com.cloudant.client.api.CloudantClient;
import com.cloudant.client.api.Database;
import org.json.JSONObject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
/*
{
"deviceid":"gas-sensor1",
"protocol":"mqtt",
"data":"{temp:25,pressure:35}",
"status":"Warning"
}
*/
public class IotCloudantConsumer implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(IotCloudantConsumer.class);
@Autowired
private IotRepository iotRepository;
CloudantClient client = ClientBuilder.account("23204af3-2c33-4bfb-bbc4-f55bbe1902ea-bluemix")
.iamApiKey("B2DtIBIPIP1qhFt6swrl-0nbQZcY6ZB1SVti_9zKX832")
.build();
Database db = client.database("sensordb",false);
@Override
public void accept (Map map)
{
LOGGER.info("Creating the Iot sensor info", map);
JSONObject iotdata = new JSONObject();
iotdata.put("deviceid","gas-sensor1");
iotdata.put("protocol","mqtt");
iotdata.put("data","{temp:25,pressure:35}");
iotdata.put("status","Warning");
IotSensor sensorinfo = new IotSensor (map.get("deviceid"), (map.get(
"protocol")), map.get("data"), map.get("protocol"));
db.save(iotdata);
}
}
Listing 7-3
CloudantConsumer.java
```

The `EventStreamsSupplier` function connects to the IBM event streams and gets the data in the `sensors-topic`. It then stores this data in the Cloudant `sensordb`.

Step 8: Deploy the Spring Cloud Function to Knative on the Kubernetes cluster. Refer to Chapter [2](#526597_1_En_2_Chapter.xhtml) for deploying Spring Cloud Function on Knative.

Step 9: Integrate event streams to invoke Spring Cloud Function. Invoke the `EventStreamSupplier` function. When running the function locally, you will get the output in Figure [7-22](#526597_1_En_7_Chapter.xhtml#Fig22).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig22_HTML.png)

A screenshot of the output of the program event streams supplier function running locally.

Figure 7-22

Running the EventStreamsSupplier locally

Figure [7-23](#526597_1_En_7_Chapter.xhtml#Fig23) shows the information that is stored in Cloudant DB.

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig23_HTML.png)

A screenshot of the sensor D B program. It selects the database option with save changes. It has options to upload an attachment, clone the database and delete.

Figure 7-23

IoT data stored in CloudantDB

This section explored the world of IoT from an IBM Cloud and IBM Watson IoT perspective. Figure [7-24](#526597_1_En_7_Chapter.xhtml#Fig24) shows the various components of the IoT platform that were covered.

You also saw that you can build functions with Spring Cloud Function and deploy them on IBM Cloud on Kubernetes.

You subscribed to IBM Cloud, created a device in the IoT platform, created a Kubernetes cluster, created a Cloudant database, created a function to post the data into the Cloudant database, connected event streams to the function, and connected the Cloudant database to the IoT platform. There are lots of things to configure in order to set up an end-to-end flow. See Figure [7-24](#526597_1_En_7_Chapter.xhtml#Fig24).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig24_HTML.jpg)

A flow diagram of Watson's end-to-end flow. Context starts from I o T devices leading to Watson I o T platform service, Event streams, and dashboard. Finally leads to customer systems, app developer architect, and I B M cloud.

Figure 7-24

Watson IoT context

## 7.2 Enabling Healthcare with Spring Cloud Function and Big Data Pipelines

Healthcare is dependent on data. Be it patient records, image recognition, genomics, tracking wearables, and using sensors, a healthcare system is bombarded with data. Figure [7-25](#526597_1_En_7_Chapter.xhtml#Fig25) conceptualizes Big Data in healthcare.

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig25_HTML.jpg)

A schema of healthcare context has circles with big data. The central part has aggregation, analysis, and action flowing from the top to the bottom. The aggregation is further connected to other components.

Figure 7-25

Healthcare context Source: [*https://www.researchgate.net/publication/330249318/figure/fig1/AS:713182909829120@1547047464835/Conceptualizing-big-data-in-healthcare-Health-system-data-are-aggregated-with-data.png*](https://www.researchgate.net/publication/330249318/figure/fig1/AS:713182909829120%253F1547047464835/Conceptualizing-big-data-in-healthcare-Health-system-data-are-aggregated-with-data.png) `(Creative commons license https://creativecommons.org/licenses/by/4.0/)`

The use case here is that a patient walks into a hospital complaining of a pain in the back, near the spine. The pain has been persistent and they want a quick diagnosis.

The process:

1.  The patient is admitted into the hospital by means of a patient portal and check-in process.

2.  Initial patient analysis is done using EHRs and patient-provided information, such as quality of life, pain, expectations, and so on.

3.  The doctor recommends imaging and laboratory tests.

4.  The patient is provided with a smart wearable that tracks their movement in the hospital.

5.  Once the imaging, laboratory tests, and other pertinent information comes in, the doctors use their smart AI diagnostic portal to diagnose possible conditions.

6.  The doctor informs the patient of the diagnosis.

Let’s now look at the systems involved in this process:

*   Patient portal for intake of patient information (data acquisition)

*   Intake and aggregate patient surveys and patient history (data aggregation)

*   Labs and imaging (image data)

*   Diagnosis (data analysis and AI recommendations)

*   Smart wearables (IoT streaming data)

Translating this processes into a cloud architecture view, you can see that Spring Cloud Function plays an important role in all these processes. It is therefore essential for the functions to be portable so that the best cloud providers can be used in each case. See Figure [7-26](#526597_1_En_7_Chapter.xhtml#Fig26).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig26_HTML.jpg)

A schema to depict the spring cloud function application. It has 5 components listed patient portal, initial patient analysis, labs and imaging, diagnosis, and smart bands and devices. Icons related to these components are beside.

Figure 7-26

Applications of Spring Cloud Function in the various healthcare flows

## 7.3 Conversational AI in Retail Using Spring Cloud Function

AI and retail are inextricably tied at the hip. Humans enjoy shopping, mall strolling, online shopping, and just online browsing. They constantly encounter AI by way of targeted advertising, product recommendations, and product placements. Many of these features are run by robots. This means that AI and retail are converging.

Based on Fortune business insights, the retail AI market is expected to reach $19.9 billion by 2027, with a growth rate of 34.4 percent from 2020 to 2027 (Source: [`https://www.meticulousresearch.com/product/artificial-intelligence-in-retail-market-4979`](https://www.meticulousresearch.com/product/artificial-intelligence-in-retail-market-4979)).

Some of the top ways that AI is used in retail include:

*   Predictive analytics using Big Data

*   Demand forecasting

*   Visual searching

*   Recommender systems

*   Chatbots

*   Augmented reality

*   Brand management

COVID-19 changed the way that AI interacted with retail. AI powered by chatbots rose to prominence.

Chatbots—or conversational AI—in fact became the AI interface for retail. Most communication with a product or support team is initiated using an AI interactive session. Better customer experience through these chatbots is of paramount importance to retailers. If the chatbots were bad, the customer would move on to another retailer. So brand management, recommendations, predictions, and forecasting all relied on how the chatbot interacted with the customers and if it extracted the right information to provide the customers with the best experience.

IBM, AWS, Azure, Google, and other cloud providers focused their attention on capturing the conversational AI market, as every retailer was clamoring for an edge with these customer interactions.

You will look at how IBM approached conversational AI and at how Spring Cloud Function plays a role in connecting systems with people.

The IBM conversational AI offering includes a wide array of products on the IBM Cloud and on cloud paks. Cloud paks are software that can be deployed to any cloud.

IBM Cloud provides both generic Kubernetes and Red Hat OpenShift Kubernetes, while the cloud paks are offered on Red Hat OpenShift. Spring Cloud Function can be deployed on any Kubernetes offering.

Figure [7-27](#526597_1_En_7_Chapter.xhtml#Fig27) shows a conceptual representation of conversational AI.

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig27_HTML.png)

A schema of artificial intelligence conversational concepts. It has data sources, conversational artificial intelligence processing, conversation channels, and outcomes. All these have sub-components.

Figure 7-27

Conversational AI concept

By translating this concept into an IBM Cloud-based architecture, you get Figure [7-28](#526597_1_En_7_Chapter.xhtml#Fig28).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig28_HTML.jpg)

A schema of artificial intelligence conversational system. It starts with conversational endpoints, then to EDGE services, I B M cloud, and to the user. The I B M cloud has components associated with it.

Figure 7-28

Conversational AI systems view

### 7.3.1 Components of Conversational AI Solutions

1.  Edge services

    Edge services allow the flow of data on the Internet. These services include DNS, CDNs, firewalls, load balancers, and so on.

2.  IBM Watson Assistant

    Watson Assistant enables you to create virtual agents and chatbots that combine machine learning, natural language processing, and graphical tools to design your conversational flows. Additional information is available at [`https://www.ibm.com/products/watson-assistant`](https://www.ibm.com/products/watson-assistant).

3.  IBM Watson Discovery Services

    Watson Discovery Services helps you ingest, parse, index, and annotate content. The ingested data can come from internal and external data sources. Additional information is available at [`https://www.ibm.com/cloud/watson-discovery`](https://www.ibm.com/cloud/watson-discovery).

4.  Watson Speech to Text

    Watson Speech to Text converts voice to text. Additional information is available at [`https://www.ibm.com/cloud/watson-speech-to-text`](https://www.ibm.com/cloud/watson-speech-to-text).

5.  Spring Cloud Function deployed on Knative on OpenShift (IBM Cloud)

    Spring Cloud Function can be deployed on OpenShift in the IBM Cloud. Knative has to be configured on OpenShift. This process is outlined in the Knative deployment discussion throughout this book.

6.  DB2 on cloud

    This is a fully managed SQL database on the cloud. Information about DB2 on cloud can be found at [`https://www.ibm.com/cloud/db2-on-cloud`](https://www.ibm.com/cloud/db2-on-cloud).

7.  Cloudant DB

    This is a fully managed distributed database ideal for web and mobile apps and storing IoT or mobile data. More information can be found at [`https://www.ibm.com/cloud/cloudant`](https://www.ibm.com/cloud/cloudant).

### 7.3.2 Watson Assistant Webhooks and Spring Cloud Function

Once you create and deploy a Spring Cloud Function, you need to be able to call it from the Watson Assistant. This can be done by means of Webhooks.

The following requirements need to be met for Webhooks:

*   The call must be a `POST` HTTP request. You can use your consumer function here.

*   The request body must be a JSON object (`Content-Type: application/json`).

*   The response must be a JSON object (`Accept: application/json`).

*   The call must return in eight seconds or fewer. This is an important requirement and can easily be met with Spring Cloud Function.

### 7.3.3 Implementing the Watson Assistant with Spring Cloud Function

Step 1: Write the Spring Cloud Function code. The Watson Assistant requires an OpenAPI specification. Add the code in this section to your `Pom.xml` file.

Listing [7-6](#526597_1_En_7_Chapter.xhtml#PC14) shows the dependencies.

```
org.springdoc
springdoc-openapi-ui
1.6.11

Listing 7-6
Open API Dependencies to Connect to the Watson Assistant
```

Now you need to create Spring Cloud Function code for iMac inventory. The model allows Watson to connect to and get a response. See Listing [7-7](#526597_1_En_7_Chapter.xhtml#PC15).

```
package com.kubeforce.watsonimacs;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;
@Entity
@Table(name= "inventory")
public class Inventory {
@Id
@GeneratedValue(generator = "UUID")
private Long id;
private String name;
private int inventoryid;
private String description;
private String quantity;
public Inventory(String name, int invid, String description, String quantity)
{
this.name = name;
this.inventoryid = invid;
this.description = description;
this.quantity = quantity;
}
public Inventory() {
}
public String getName ()
{
return name;
}
public void setName (String name)
{
this.name = name;
}
public int getEmployeeIdentifier ()
{
return inventoryid;
}
public void setCustomerIdentifier (int invid)
{
this.inventoryid = invid;
}
public String getEmail ()
{
return description;
}
public void setEmail (String email)
{
this.description = description;
}
public String getSalary ()
{
return quantity;
}
public void setSalary (String salary)
{
this.quantity = quantity;
}
public Long getId ()
{
return id;
}
public void setId (Long id)
{
this.id = id;
}
}
Listing 7-7
inventory.java
```

Listing [7-8](#526597_1_En_7_Chapter.xhtml#PC16) shows the `InventoryConsumer` file.

```
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Map;
import java.util.function.Consumer;
public class InventoryConsumer implements Consumer> {
public static final Logger LOGGER = LoggerFactory.getLogger(InventoryConsumer.class);
@Autowired
private InventoryRepository InventoryRepository;
@Override
public void accept (Map map)
{
LOGGER.info("Creating the inventory", map);
Inventory inventory = new Inventory (map.get("name"), Integer.parseInt(map.get(
"inventoryid")), map.get("description"), map.get("quantity"));
InventoryRepository.save(inventory);
}
}
Listing 7-8
InventoryConsumer.java
```

Listing [7-9](#526597_1_En_7_Chapter.xhtml#PC17) shows the `InventoryFunction` file.

```
package com.kubeforce.watsonimacs;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.Optional;
import java.util.function.Function;
public class InventoryFunction implements Function  {
@Autowired
private InventoryRepository inventoryRepository;
@Override
public Inventory apply (Long s)
{
Optional inventoryOptional = inventoryRepository.findById(s);
if (inventoryOptional.isPresent()) {
return inventoryOptional.get();
}
return null;
}
}
Listing 7-9
InventoryFunction.java
```

Listing [7-10](#526597_1_En_7_Chapter.xhtml#PC18) shows the `InventorySupplier` file.

```
package com.kubeforce.watsonimacs;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import java.util.List;
import java.util.function.Supplier;
@Component
public class InventorySupplier implements Supplier
{
public static final Logger LOGGER = LoggerFactory.getLogger(InventorySupplier.class);
@Autowired
private InventoryRepository InventoryRepository;
@Override
public Inventory get ()
{
List inventories = InventoryRepository.findAll();
LOGGER.info("Getting the computer of our choice - ", inventories);
return inventories.get(0);
}
}
Listing 7-10
InventorySupplier.java
```

Deploy this code into an IBM Kubernetes Knative instance, as shown in Chapter [2](#526597_1_En_2_Chapter.xhtml) for Knative.

This code will now have an OpenAPI definition. This will allow the Watson Assistant to communicate with your function You can use this to configure the custom extensions in the Watson Assistant.

Step 2: Configure the Watson Assistant.

1.  Define the scope of the chatbot:
    1.  Example use cases
        1.  Tell the user what Apple products are available.

        2.  Tell them how many iMacs are in stock.

2.  Log in to the IBM Cloud ([`http://cloud.ibm.com`](http://cloud.ibm.com)).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig29_HTML.png)

A screenshot of the I B M cloud dashboard. It has different options including building a dashboard, resource summary, planned maintenance, and I B M cloud status.

Figure 7-29

IBM Cloud dashboard

1.  Sign up for the Watson Assistant and create an instance.

You can arrive at the Watson Assistant dashboard by searching the IBM Cloud portal or using this link [`https://cloud.ibm.com/catalog/services/watson-assistant`](https://cloud.ibm.com/catalog/services/watson-assistant).

Pick your plan and choose a location, as shown in Figure [7-30](#526597_1_En_7_Chapter.xhtml#Fig30).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig30_HTML.jpg)

A screenshot of I B M cloud with Watson assistant signup page. It has different options including the plan, features, and pricing details.

Figure 7-30

The Watson Assistant signup page

Click the Create button to proceed to the next step of personalizing your assistant.

1.  Create and personalize your assistant.

Choose the name you want for your assistant and click Next. See Figure [7-31](#526597_1_En_7_Chapter.xhtml#Fig31).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig31_HTML.jpg)

A screenshot of the create your first assistance page. It has options to enter the assistant name, description, and language.

Figure 7-31

Create your assistant

Choose your options. You will be provided a preview of the chat window.

You can choose the type of deployment you prefer. This example uses Facebook, as it provides a nice interface. See Figure [7-32](#526597_1_En_7_Chapter.xhtml#Fig32).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig32_HTML.jpg)

A screenshot of the personalize your assistant creation. It has options to choose the instance where the assistance needs to be deployed and other options.

Figure 7-32

Create an assistant with a template

Fill in the Tell Us About Yourself section to your liking. See Figure [7-33](#526597_1_En_7_Chapter.xhtml#Fig33).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig33_HTML.jpg)

A screenshot of the Watson Personalize your assistance page. It has the option to personalize the assistant with Facebook and tell us about yourself.

Figure 7-33

Assistant with a Facebook template

Click Create to arrive at the next window. The assistant should now be configured successfully, as shown in Figure [7-34](#526597_1_En_7_Chapter.xhtml#Fig34).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig34_HTML.png)

A screenshot of the home page of the new Watson assistant. It has the option to get started and to create a conversation.

Figure 7-34

The assistant has been created successfully

1.  Create an action.

Create an action that helps you to interact with the customers. You can do this by choosing Create Your First Action under the Create a Conversation section of the page. You can choose to use a template or create this from scratch.

Choose the options highlighted in Figure [7-35](#526597_1_En_7_Chapter.xhtml#Fig35) to create your action.

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig35_HTML.jpg)

A screenshot to create your action. It highlights the text and the steps to create action. The steps are set by the assistant, name, choose to start from scratch, choose options, and fill in options 1 and 2.

Figure 7-35

Flow of activities to create your action

Continue following the instructions to complete the action. You can click Preview to look at how the conversation flows and modify it to fit your needs. You now need to tie this to the Inventory function you created in Step 1 to let the bot search the inventory and respond. See Figure [7-36](#526597_1_En_7_Chapter.xhtml#Fig36).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig36_HTML.jpg)

A screenshot of the Greet customer page. It has options to create an action in 2 steps by selecting the options available.

Figure 7-36

Create an action

You now have to create an integration using the Integrations button shown at the bottom-left side of the screen in Figure [7-37](#526597_1_En_7_Chapter.xhtml#Fig37). This is available on the Home button.

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig37_HTML.jpg)

A screenshot of the I B M Watson assistant page. It has options for home, actions, preview, publish, environments, and analyze.

Figure 7-37

Add an integration to support calling the Spring Cloud Function

1.  Build a custom extension.

The Integrations button will take you to the Build Custom Integrations dashboard, as shown in Figure [7-38](#526597_1_En_7_Chapter.xhtml#Fig38).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig38_HTML.png)

A screenshot of the Watson assistant page. It displays the Integration catalog with the option to build custom extensions.

Figure 7-38

Build a custom extension from the integration catalog

Provide your extension name and a description, as shown in Figure [7-39](#526597_1_En_7_Chapter.xhtml#Fig39).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig39_HTML.png)

A screenshot of the I B M Watson assistant page. It displays the custom extension page with options to enter the basic information.

Figure 7-39

Provide the custom extension information

Pick the function that you deployed. Once you provide the URL, it will give you some options to choose from, as shown in Figure [7-40](#526597_1_En_7_Chapter.xhtml#Fig40).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig40_HTML.jpg)

A screenshot of the I B M Watson assistant. It displays the custom extension page with options to review extensions including servers and operations.

Figure 7-40

Add Open API URL for the deployed Spring Cloud Function

Apply the custom extension, as shown in Figure [7-41](#526597_1_En_7_Chapter.xhtml#Fig41).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig41_HTML.jpg)

A screenshot of the I B M Watson assistant. It displays the extension setup page with check inventory in extensions and inventory supplier in operations tabs.

Figure 7-41

Apply the custom extension

The custom extension is now available for the action, as shown in Figure [7-42](#526597_1_En_7_Chapter.xhtml#Fig42).

![](images/526597_1_En_7_Chapter/526597_1_En_7_Fig42_HTML.png)

A screenshot of the I B M Watson assistant. It displays the completed custom extension page with a confirmation that step 1 is taken without condition.

Figure 7-42

The custom extension is available for the action

You can now can retest your chat interface by going back to the chat that you created.

See GitHub at [`https://github.com/banup-kubeforce/Watson-imacs.git`](https://github.com/banup-kubeforce/Watson-imacs.git) for additional information. Deploy the code in your environment and integrate. This is a fun exercise.

## 7.4 Summary

This chapter looked at some real-world use cases and deployed Spring Cloud Function in IBM Cloud.

This demonstrates the true "write once, deploy anywhere" capability of Spring Cloud Function.

The chapter also looked at what IBM Cloud has to offer. IBM Cloud is versatile and has many products that have been built and optimized over the years. You saw how IBM Cloud can be used in the oil and gas, healthcare, and retail markets.

There are many other use cases in which you can apply Spring Cloud Function, and it can be an alternative to a microservices architecture or can coexist with one. The decision to use functions over microservices needs to be carefully analyzed for cost, scalability, and performance before deciding on an approach.

Index A Add applications create streams data pipeline log component prebuilt templates properties in SCDF SpEL Add Device page Added Dependencies section Ahead of Time (AOT) AI/ML process cloud functions cloud providers compute and storage requirements data pipeline deploying endpoint feature engineering flow Google Java and Python language popularity lifecycle model evaluation monitoring requirements Spring Cloud Function Spring framework train model AKS cluster AKS Knative Apache Kafka Apache MXNet Apache.org App deployment Application.properties Apps utilization ArgoCD argocd-server GitOps installation log in namespace creation password argocd-server Argument Assistant creation AWS account AWS CLI AWS EKS cluster AWS Glue components Kinesis Spring Cloud Function Studio AWS Greengrass AWS IoT Greengrass Lambda function management console MQTT message on-premises sample function AWS Kinesis AWS Lambda cloud providers dashboard testing deploying function creation function subscription IDE PayrollAwsApplication.java pom.xml File runtime settings test results test tab tweaking Upload From button AWS Lambda serverless function AWS SAM framework AWS SDK dependencies Azure CLI Portal subscription Azure AKS cluster Azure Blob store Azure Container Registry Azure functions account CLI console dashboard deploying EmployeeConsumerHandler.java IDE run of the test subscription test with input Azure IoT components Edge Edge device hub B Big data Boilerplate Brand management Bucket C Character recognition Chatbots CI/CD process ArgoCD GitHub Actions target platform CLI commands Cloudant CloudantConsumer.java CloudantConnector.java Cloudant DB Cloudant page Cloud bursting Cloud Foundry environment Cloud providers Cloud-ready gateways Cloud Run Cloud storage Cluster clusterconfig.yaml cluster.yaml Command-Line Utilities Common tasks Components Compute Engine Computer vision Concept drift Consumer Continuous delivery (CD) Continuous integration (CI) See alsoCI/CD process Conversational AI components concept and retail in retail Watson Assistant Cost-effective alternative COVID-19 Cracks Create button Create Database Create Streams CRUD operations Curl Custom extension information D Datacenters Data drift Data event pipelines acquire data implementation load data store/ingest data transform data Dataflow Dataframe Data integrity Data lakes Data pipelines coding data cleaning data collection data labeling process SCDF wire up Dataset Data warehouses Decoupling Deep Java Library (DJL) abstraction layer dependencies kitten image layers probabilities serving Spring Cloud Function TensorFlow Deep neural networks Default domain Dependencies Deploying AWS Lambda to Azure Functions GCP Cloud Functions to Knative Deploying models Deployment desiredCapacity DevOps djl-serving Docker hub image push Dockerfile Dockerhub Docker Push Domain Specific Language (DSL) Drug discovery E Eclipse Edge services EKS cluster EKS console Eksctl CLI EmployeeConsumer function EmployeeConsumerHandler Employee Entity Model Employee model employeeSupplier Event Streams page EventStreamsSupplier function EventStreamsSupplier.java External IP F Facebook template Feature engineering Firecracker Fleet management Fortune business Free cluster Free tier Function definition FunctionInvoker class Functions as a Service (FaaS) code portability issues component architecture description enterprise application implementation migration ROI parts serverless functions, enterprise application G Gas pipeline, IoT implementation Gcloud CLI GCP Cloud Functions deploying dependencies function subscription Genomics GET function Get Function URL Get Started GitHub GitHub actions GitHub Secrets store GitOps GKE cluster GKE dashboard Glue Studio Google Google account googleapis Google CLI Google Cloud CLI Google Cloud Dataflow cloud pub/sub instance cloud storage data pipeline ProducerFunction pub/sub template vehiclebucket1 Google Cloud Functions Azure Functions dashboard Gcloud CLI execution PayrollGcpApplication.java pom.xml file subscription Google Clouds GKE dashboard Google Cloud Storage Google Cloud Storage Bucket Google’s libraries GraalVMs Graphical representation H HAProxy H2 console Healthcare Helm chart HTTP method Hub Humidity Hybrid cloud Hyperscalers I IBM API Connect (APIC) IBM Cloud IBM Cloudant DB IBM cloud functions IBM Cloud Watson IBM Event Streams IBM Event Streams Configuration IBM Kubernetes IBM Watson Assistant Discovery Services IoT Platform iMac inventory Image recognition Ingress Information INSERT statement Integrations IntelliJ Internet of Things (IoT) cluster description device market gateways implementation pricing plan sensors Spring Cloud Function Spring implementation survey InventoryConsumer.java Inventory function InventoryFunction.java inventory.java InventorySupplier.java iot.connection.string variable IoTConsumer.java IoT Edge device IoT Platform IoT Process flow Istio J JAR files Java Java-based Lambda JDK JDK 8 JDK 11 Jenkins JSON file JSON object JSON response Jupyter notebook cell K Kafka Kinesis application properties for data dependencies model creation producer creation function implementation run the function Knative CLI components crds deploying growth install and configure portable serverless containers provider operators serverless workloads services serving Kourier Kubeconfig kubectl Kubernetes Kubernetes IN Docker (KIND) L Labeling raw data Lambda Lambda serverless functions Language popularity Leaks Linux Containers (LXC) LoadBalancer Load data Local machine M Magic DNS main.py Manufacturing costs Manufacturing plant process flow Marketplace Master branch Maven maven clean command Maven dependencies Maven package Mean Time To Market (MTTM) MessageEntity class Microcontrollers Microservices MNIST mnist-soc Model evaluation Model server Model Serving Model training MongoDB MqttConsumer.java MQTT message MqttPublish class MqttPublish.java MqttSubscriber.java Multi-cloud adoption report MYSQL N Natural gas pipelines gateways sensors transmission Network components New Project New Workflow NodePort Notebook instance O Object stores ONNX On-premise resources On-premises OnStar OpenAPI OpenShift Oracle P Paradigms Payroll application PayrollApplication.java PayrollAwsApplication.java Payroll cluster Payroll function PayrollGcpApplication.java payroll.yaml file Penske pH levels Pivotal Cloud Foundry (PCF) Pivotal Ready Architecture (PRA) POM dependencies pom.xml changes pom.xml File Portable serverless container Portfolio POST-based test POST HTTP Postman Power BI Prebuilt starters Prebuilt templates Prerequisites Pre-trained models Processor ProducerFunction class Project root folder PubSubPublisher class PyCharm Python PyTorch Q QueueSender component R RabbitMQ any messages configurations dependencies pipeline process prerequisites QueueSender component SenderConfig component RDBM Red Hat Code Red Hat OpenShift Repair/collision REST APIs Return on investment (ROI) Robotics S SaaS platform SaaS provider SageMaker SAP ECC savedmodel3.zip S3 bucket SenderConfig Component senderFunction Sensor data Sensors Sensor-topic Serial number Serverless container portability issue Serverless functions Serverless functions, enterprise application average startup time cloud infrastructure infrastructure savings interfaces non-proprietary payroll application Serving Set Up a Workflow Yourself Sink S3 integration SmartSense Smart wearables Speech recognition SpringApplication Spring-based microservices Spring Boot code employee model creation scaffolding steps test the function write the consumer write the function write the supplier Spring Cloud Data Flow (SCDF) add application Spring Cloud Function Spring Cloud Function AI/ML AWS Lambda Azure Functions Azure IoT Edge CI/CD process SeeCI/CD process cloud offerings common tasks configuration file data.sql deploying SeeDeploying deployment with DJL GCP GitHub Actions goals Google Cloud Dataflow Google Cloud Functions H2 database HRM system hyperscaler relationship IoT SeeInternet of Things (IoT) JDK 11 Knative Knative and EKS, AWS Knative and GKE, GCP Knative on Azure AKS libraries OCP on-premises SCDF schema.sql serverless providers Spring Boot step-by-step approach VMware Tanzu Watson Assistant Spring Cloud Streams creation and deployment graphical representation sample dashboard Spring Data Spring Expression Language (SpEL) Spring Framework DJL hybrid approach Spring Cloud Function Spring IDE Spring implementation Spring Initializer Spring Integration Spring Kafka Spring MQTT Spring Web Spring XD SQL Server sslip.io S3 storage Step-by-step approach Storage bucket Supervised learning activities S3upload.java Supplier function SYNC button T Tableau Tagging Tanzu Kubernetes grid (TKG) Target datastore Target folder Target platform Temperature Temperature fluctuations TensorFlow AWS Lambda DJL Test Event section Test image TFLite Third-party gateway TKG download Total Cost of Ownership (TCO) TrackDetail class Train model Transform data Twitter U Ubuntu VM Unauthenticated device V Value equation Variance vehicledatapipeline Vehicledatastream Virtual Box VMware VMware Tanzu VS Code VS Studio Code W Watson Assistant dashboard signup page with Spring Cloud Function Webhooks Watson IoT context Watson Speech Webhooks Windows Subsystem for Linux (WSL) Work flow code Workflow points Wrap the sender X XGBoost ML algorithm XRAYFunction.java Y, Z YAML execution YAML file
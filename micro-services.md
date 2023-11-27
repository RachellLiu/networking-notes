# Micro-services

Micro-service architecture is an architectural style that structures an application as a collection of services that are:

- Independently deployable
- Loosely coupled
- Organized around business capabilities
- Owned by a small team
- Each service consists of one or more subdomains and is owned by the team (or teams) that owns the subdomains.

The microservice architecture enables an organization to deliver large, complex applications rapidly, frequently, reliably and sustainably - a necessity for competing and winning in today’s world.

In order to be independently deployable each service typically has its own source code repository and its own deployment pipeline, which builds, tests and deploys the service.

Some system operations will be local (implemented by a single service), while others will be distributed across multiple services. A distributed operation is implemented using either synchronously using a protocol such as HTTP/REST or asynchronously using a message broker, such as Apache Kafka.

## Advantages of microservices

The advantages of microservices seem strong enough to have convinced some big enterprise players such as Amazon, Netflix, and eBay to adopt the methodology. Compared to more monolithic design structures, microservices offer:

- **Improved fault isolation**: Larger applications can remain mostly unaffected by the failure of a single module.
- **Eliminate vendor or technology lock-in**: Microservices provide the flexibility to try out a new technology stack on an individual service as needed. There won’t be as many dependency concerns and rolling back changes becomes much easier. With less code in play, there is more flexibility.
- **Ease of understanding**: With added simplicity, developers can better understand the functionality of a service.
- **Smaller and faster deployments**: Smaller codebases and scope = quicker deployments, which also allow you to start to explore the benefits of Continuous Deployment.
- **Scalability**: Since your services are separate, you can more easily scale the most needed ones at the appropriate times, as opposed to the whole application. When done correctly, this can impact cost savings.

## Disadvantages of microservices

- **Communication between services is complex**: Since everything is now an independent service, you have to carefully handle requests traveling between your modules. In one such scenario, developers may be forced to write extra code to avoid disruption. Over time, complications will arise when remote calls experience latency.
- **More services equals more resources**: Multiple databases and transaction management can be painful.
- **Global testing is difficult**: Testing a microservices-based application can be cumbersome. In a monolithic approach, we would just need to launch our WAR on an application server and ensure its connectivity with the underlying database. With microservices, each dependent service needs to be confirmed before testing can occur.
- **Debugging problems can be harder**: Each service has its own set of logs to go through. Log, logs, and more logs.
- **Deployment challengers**: The product may need coordination among multiple services, which may not be as straightforward as deploying a WAR in a container.
- **Large vs small product companies**: Microservices are great for large companies, but can be slower to implement and too complicated for small companies who need to create and iterate quickly, and don’t want to get bogged down in complex orchestration.

## Deployment of microservices

The best way to deploy microservices-based applications is within containers, which are complete virtual operating system environments that provide processes with isolation and dedicated access to underlying hardware resources. One of the biggest names in container solutions right now is [Docker](https://cloudacademy.com/course/introduction-to-docker/getting-started-6/).

Virtual machines from infrastructure providers like Amazon Web Services (AWS) can also work well for microservices deployments, but relatively lightweight microservices packages may not leverage the whole virtual machine, potentially reducing their cost-effectiveness.

Code deployments can also be completed using an Open Service Gateway Initiative (OSGI) bundle. In this use case, all application services will be running under one Java virtual machine, but this method comes with a management and isolation trade-off.

## Designing a microservice architecture

**Assemblage** is an architecture definition process that you can can use to define your microservice architecture. It distills your requirements into system operations and subdomains; uses the dark energy and dark matter forces to group the subdomains into services; and designs the distributed system operations.

![Assemblage](https://microservices.io/i/posts/assemblage-overview/Defining_Microservice_Architecture_V2.png)

### The architecture definition process consists of the following steps:

#### 1. **Discovering system operations**

The first step of the process distills the requirements into a set of system operations. A system operation is an invokable behavior implemented by the application. For example, an e-commerce application would typically implement operations such as createCustomer(), createOrder(), cancelOrder() and findOrderHistory(). A system operation reads and/or writes one or more business entities, a.k.a. DDD aggregates, such as Customer and Order. The system operations model the application’s black box behavior.

A system operation is technology independent. But the actual implementation will be invoked in one of several ways. It might, for example, be invoked by a HTTP request or a message. Alternatively, a system operation might be triggered by the passing of time, eg. a monthly batch job.

#### 2. **Defining subdomains**

The second step of the process defines the subdomains. A subdomain is an implementable model of a sliver of business functionality, a.k.a. business capability. Each subdomain is owned by a small team. A subdomain consists of the aggregates acted upon by system operations. In Java application, for example, a subdomain would consist of Java classes.

#### 3. **Designing services and their collaborations**

The third step defines the service architecture by grouping the subdomains to form services and designing distributed system operations using the service collaboration (e.g. Saga, API Composition, and CQRS) patterns.

The dark energy and dark matter forces drive the definition of services and the design of system operations.

The output of the third step is a candidate service architecture that is either a monolithic architecture (i.e. a single service) or a microservice architecture (two or more services). The architecture documentation includes a microservice canvas for each service.

#### 4. **Evaluating a microservice architecture**

The fourth step evaluates the architecture to identify architectural issues/smells that are potential violations of the dark energy and dark matter forces.

Examples of architectural issues/smells include:

- Teams that lack of autonomy because too many teams work together on the same service
- Services that repeatedly change in lock step due to design time coupling
- Operation that have low availability and high latency because they span too many service and require too many network roundtrips

The output of fourth step is a list of potential architectural issues.

#### 5. **Refactoring a microservice architecture**

The fifth and final step refactors the architecture to eliminate architecture smells identified in the previous step. There are four levels of refactorings:

- System operations - e.g. change collaboration patterns
- Services - eg. move subdomains between services
- Subdomains - e.g. split subdomains
- System operation specifications - e.g. to reduce runtime coupling

The output of the fifth step is an improved microservice architecture.

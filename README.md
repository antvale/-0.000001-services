# 0.000001-services=microservices
You can find here some code samples about how you can develop simple  microservices using several technologies.

Let me start the first section summarizing common microservice definitions and principles that you can find typically looking at leterature and materials available on the Web.

## Principles
The principle behind the microservice ofter aren't new but are part of good design practices

### Common principles
* Teams independently architect, develop, deploy and maintain each microservice
* Each microservice often has its own datastore, which may not be 100% up to date
* Microservice is fully decentralized - no ESB, no single database,...
* Responses from microservices aren't manipulated by any intermediaary
* Microservice favors simple transport (xml/jason over rest but no soap)
* Any given instance of a microservices is stateless
* Microservices support polyglot - each team is free to pick the best technology
* Devops principles - automated setup and developers owning production support
* Use of containers, which allow for simple app packaging and fast startup time
* Use of cloud, for elastic infrastructure, platform and software services
* All data exchange b/w microservices must be through API layer or messaging - no accessing datastores cross-microservices
* Must implement high-speed messaging b/w microservices, REST + HTTP probabily isn't fast enough
* May end up duplicating data across datastores - e.g a customer's profile
* Sharing datasources across microservices introduces coupling so this is a bad practices
* Reduce the latency across the microservices (latency=eventual consistency)
* Prefer async communications to sync that lead to availability and performance issues
* Sync calls with ms are very bad - (A depends on B depends on C,..) chaining==coupling==downtime
* Avoid sync calls for read-only data by copying
* An example: Product catalog is called in sync fashion, Product, pricing, Inventory, Promotion send in async way their updates to Product Catalog, some data may be duplicated, the Product Catalog is not always consistent
* ms favors choreography to Orchestration
* Make the middle tier stateless if you can
* Push all state and configuration down to highly availabel cloud services (state service, configuration service, registry/discovery service)
* Everything should be dynamic (discovery, DNS, ...)
* Modify the massage to be less fragile to be executed many time produting the same results, a.g. apply idempotent principle
* Prefer binary protocol (apache avro, apache thrift, Oracle Portable Object Format, Google protocol Buffer) over wire (private net) that is faster
* Prefer xml/json over http to communicate with client over public net
* Build for backward compatibility versioning the services
* Smart endpoint, dumb pipes, that's, messaging should just pass messages and not manipulate them
* Messaging should provide support for many protocols (oneway, request/response, pub/sub)
* Durable and ordered messaging for point-to-point queue
* Non-durable, un-orderd, high throughput, non-brokered for event messaging
* Circuit breaker to prevent cascading failures
  * Cascading failures happen when **request-handling threads** are waiting on a response from a remote system
  * Circuit breakers make synchonous calls from another thread pool to avoid binding up request-handling threads
  * Hystrix (java-based) is well-known and solves this problem
* Use network segmentation to isolate microservice in secure zones
* Container make microservices easier but they are not a requirement
  * Container are lighweight, immutable, easy to ship, run and Management
  * Use container especially to package the application, continuous integration,  
  * Never touch a container that has already been built
  * one instance for container is typical, multiple instances make scheduling very difficult
  * some orchestration tool: Docker Swarm, Rancher, Mesos, Kubernetes,...
  * Autoscaling
  * Artifacts are immutabe container (not ear, war)
  * No more installing a JVM, app server, and then deplying the artifacts to them
  * Build the container once, deploy it anywhere
  * Container should be free of state and configuration
* Deploy constantly - even hourly
* Each microservice can be written, build, and deployed independently
* Run many versions of the same microservice concurrently
* Run many versions of each microservice in the same environment

### API reference implementation
* Jersey - RI of JAX-RS/JSR 311
* [Grizzly](https://grizzly.java.net/) - high performance I/O, fast IPC, non-blocking sockets, websocket support


### Gateway - Aggregates the responses of many microservices
* [Netty](http://netty.io/wiki/index.html)
* [Kong](https://getkong.org/about/)
* Apigee

### Need further investigations
* [Zipkin](https://github.com/openzipkin/zipkin/wiki) to trace a pages in order to see their execution path and to determine the time spent for loading for performance monitoring and analysis.

### Orchestration vs choreography
* Orchestration
  * top-down coordination of discrete actions
  * Used in centralized monolithic apps
  * Brittle - centralized by nature
  * Each action registers with centralized system - single point of failure that is not very flexible
* Choreography
  * Bottom-up coordination of discrete actions
  * Used in distributed, microservice applications
  * Resilient - distributed by nature
  * Each microservice async throws up a message that other microservices can consume

### CAP (Consistency Availability Partitioning) Theorem
* Consistency - each node shows the same data at all times
* Availabitity - Each node is available for writes at all times
* Partition Tolerance - Able to handle network outages
* see [dynamodb](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) for further details
* Practice says that the whole system never can fit all three dimension at all times
* Each microservice can be CP, AP or CA but the system as a whole is always CP or AP

### You're doing microservice if you:
* can **build** a microservice **indeplendently**
* can **release** each microservice **indeplendently**
* **don't share datasource** across microservice

### Think microservices as linux utilities (Doug Mcllroy)
* Write programs that do one thing and do it well
* Write programs to work together 
* Write programs to handle text streams because that is a universal interface

### When it is time to look at microservices
1. 100+ developers for an application
2. 5m lines of code for an application
3. Monthly or quarterly releases to production
4. \>1 quarter backlog of developement
5. \>20% developer turnover
6. The application is enough complex that the microservice-style architecture may pay off over time

### How big should be a microservice
* Focus on business capabilities (domain-driven analysis and design) not on technology layers
* Reduce complexity through modularization
* Do one thing and do it well
* Avoid inter-dipendencies (high cohesion and low coupling)
* large size service: 11-15 people e.g. Order Management
* medium size service: 4-10 people e.g. Inventory Management
* small size service: 1-3 people e.g. Order Status
* small team much better communication

## Weakness
- Technical complexity
  - They don't eliminate the complexity, it just moves it and ofter add to it
  - Monolithic applications allow you to deal with complexity in one body of code
  - Forces move to distributed computing
- Effort
  - Testing, logging, monitoring, security, versioning, etc. all become much harder
  - Polyglot exponentially increases the number of the lifecycles required
  - A lot of duplicated effort since each team is independent and goal is to minimize dependencies
- Organization
  - Most Organization are structured around horizontal technology layer- need to build a small product-focused teams
  - much higher skills required
  - many developers will not want to do production support

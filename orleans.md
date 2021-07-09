# Orleans Deep Dive Notes
### Christopher Stansbury
## Section 1 - What is Orleans
Orleans is a .NET framework for building large scale, distributed systems. Anytime you have an application and you want to be able to make good use of your hardware or, for example, take away some pressure from databases, Orleans simplifies writing these kinds of applications.  
- The basic unit of Orleans is called a grain.
- A grain is analagous to an object in OOP. Grains encapsulate state and behavior. A more apt description of grains might be "cloud native object" or "distributed object".
---
## Section 2 - Motivation for Orleans
Let's say you're building a web application with a typical architecture: the internet talks to a load balancer, which talks to multiple instances of the same application, which eventually end up talking to the database:

![Figure 1 - Basic application architecture](https://res.cloudinary.com/stansbury/image/upload/b_rgb:ffffff/v1625858138/basic-app-arch_pahnty.png)
---
*Figure 1 - Basic application architecture*


You will have multiple instances of the application due to things like redundancy and load balancing. But there are issues with this traditional architecture when dealing with distributed systems. When we're hitting the database for every request, that ends up adding some latency to our requests. Because we're hitting the database on every single read request, we're putting undue load on the database. Optimally, we would want to using the database CPU and memory cache for serving writes instead. There an also be issues with synchronicity when multiple copies of the app are writing to the same database at the same time. 


![Figure 2 - Application Architecture with Cache](https://res.cloudinary.com/stansbury/image/upload/b_rgb:ffffff/v1625857978/basic-app-arch-cache_c52xqz.png)
---
*Figure 2 - Application architecture with cache*

For read-heavy workloads, a common fix for these issues is by using a separate cache like Redis to store the read-only information. This can lead to issues with cache coherency. If you add a queue into the mix, you can solve some issues with write-heavy workloads and you application is becoming quite complex by this point. 

![Figure 3 - Application Architecture with Cache and Queue](https://res.cloudinary.com/stansbury/image/upload/b_rgb:ffffff/v1625858441/basic-app-arch-cacheandqueue_y1co7v.png)
---
*Figure 3 - Application architecture with cache and queue*

Orleans tries to solve these issues. By allowing the application instances to talk to each other, we can structure things in a way that makes them efficiently cache data and coordinate writes, removing the need for external caches and queues.

![Figure 4 - Application Architecture with Orleans](https://res.cloudinary.com/stansbury/image/upload/b_rgb:ffffff/v1625858658/orleans-app-arch_jpto6h.png)
---
*Figure 4 - Application architecture with Orleans*

---
## Section 3 - How Orleans Works
So, how do we make applications "talk to each other"? Especially on a distributed system, where scaling is a given, we need a source of truth for which versions of the application are currently running and available to communicate with. A database is the source of truth. There is a "cluster membership" table in the database that contains information about the current active clusters and allows for new clusters to communicate their active state. 

Now we have a healthy, fault-tolerant cluster. Now what do we do with it to coordinate the work amongst the clusters and remove the need for external caches and queues.

Orleans does this by dividing our application up into many little object, grains. Each grain gets an id, a class, and some persistent state. We spread grains around our cluster, loading them into memory as needed and unloading them when they become idle. We route messages to grains, so every identitical API call for a given project will point to the same object. We also make each grain single-threaded. This enables us to not have locks, makes coordinating writes to storage easier and generally produces less bugs. 

Grains provide many benefits when used in distributed applications. Since hot data is kept in memory, only cold data is in the database. Since each grain owns its own state, there is less database write contention. Since grains act like a smart, write-through cache, there are less cache coherency issues. Grains are spread out over your application instances, which provides better scalability. Since there is no need for async writes via a queue, write latency will go down. Finally less code, less infrastructure, less error handling and retry logic means less complexity. 
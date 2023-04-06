# Web Architecture

A system mostly comprises of:

![Diagram](/assets/diagram.png)

## 1. Browser / Client

- Allows you to interact with the web and access different websites / web apps.
- There are different browsers like Chrome, Safari and Firebox. A client can also be a mobile / watch app like the Facebook app.
- When you type the URL of the website you want to access in your browser, a **request** to a server is made for the files that builds up the website.
- When the server receives the request from the browser, it will send back a **response** with the appropriate JS, HTML and CSS files that your browser needs to use in order to build the website.
- This doesn't scale well when it comes to the billions of users in the world accessing the billions of websites. This is where you have to introduce some more pieces to the system.

## 2. DNS

_Domain Name System_

- When you try to access a website you use a name that is human-readable. This domain name maps to an IP address (the computer recognizes it as a unique address somewhere on the Web).
- DNS helps figure out what specific **IP address** you need in order to access the website server so you can get the website's files.
- Many different companies that own a website have millions of IP addresses because there are numerous servers in the world that can house all the different files to help build up that website. A website doesn't specifically need one specific IP address.
- DNS has to maintain ipv4 and ipv6.

## 3. Web Servers

- A web server is a sever that takes some request about a web app and then sends back the appropriate files to the client which then uses to build the website or a _portion_ of a website (if it receives JSON files).
- A web server deliveries specific functionality or purpose such appropriate data for a particular user.
- The core things that make a web server work are:
  - **Application logic**: code that applies some kind of transformation steps on some specific data. Usually it lives inside the web sever.
  - **Database**: holds data in specific categories.
- In bigger apps, there are millions of users all trying to access a web server to get the required data for them: enormous amount of traffic.
- In order for a web server to function efficiently it needs to consume **resources**:
  - storage
  - memory (RAM)
  - processing power (CPU)
  - network speed
- The more traffic there is, the more these resources are being consumed. How do you improve the capacity? There are two main types of **scaling**:
  1. _**Vertical**_ => increase these different resources available on this one server.
  2. _**Horizontal**_ => clone the actual web server multiple times virtually.

### Horizontal scaling

By cloning out your web server, you're going to clone application logic. However, it doesn't make sense to duplicate the data in the database. All these web servers copies are going to connect to the same database in order to serve the actual code and functions that it has to.

## 4. Load Balancer

Without a load balancer we have no real way to figure out how to properly manage the actual requests coming in.

- Its purpose is to _evenly_ distribute the load of requests between the servers available (assuming latency[^1] is always the same - in real life, different requests take different times).
- The load balancer acts as the _entry point_ into the system. We're not communicating directly to the server, instead we're going to communicate from the browser to the load balancer, and then the load balancer is going to distribute the requests.
- A very typical strategy for how to actually distribute the load is **Round Robin**. It goes in order on the list of servers. As requests come in, it goes to the next corresponding server in the list. We're, in theory, getting a balanced load because we're assuming the latency is the same between requests which means that if we evenly distribute these requests, all the servers should be sharing the load equally.
- Servers might go down. The load balancer is smart enough to know that if a server goes down, it's not going to route any request to it. Instead, it's going to distribute the load with the remaining servers that are up.
- The load balancer is the responsible for balancing the traffic that's coming in so your system is reliable. You want your servers to be evenly receiving the amount of requests so the servers are not overwhelmed.

[^1]: **Latency**: the time it takes for the browser to fire a request and then get back a response from the server.

### Session persistence

A **consistent connection** between the client and the server has to be maintained because the client is constantly requesting information in order to receive new data that's specific to the session a user has opened.
A client can't jump between multiple servers, one server is the one who is aware of what it has given the session so far. Load balancers know how to maintain these sessions.
If a server goes down and it has a session and connection already maintained and established with a load balancer, the load balancer knows that it now needs to distribute the connection. The load balancer knows what the available servers are and tries to route the traffic evenly. Sessions information can be kept in the _client_ which is sent in the request and then the server that takes over keeps up on what the previous server was doing so it can take over the session - it's going to take some computation time. Session information can also be saved in the _load balancer_.

## 5. Databases

- Web servers house our business logic which is meant to action on some data in order to provide an expected outcome.
- Data is stored inside the database. A database's purpose is to store and retrieve data. The larger the data gets, the longer the retrieval takes: it ends up being the most latency heavy step in our system.
- There are some optimizations for databases to make it faster.

## 6. Caching Service

- It's also a form of storage.
- It prioritizes speed of retrieval over amount of data stored so data can be retrieved faster.
- Used to store high-impact data that needs to be pulled out fast. For example, region specific data.
- Used to skip communicating to the database - as long as the data in the Cache is still relevant. We don't want to repeat that extra work.
- Can be added to numerous steps inside of the system: you add it anywhere data has to be sent which takes time.

Main things you target inside your systems design:

- RELIABILITY
- AVAILABILITY
- SPEED

## 7. Job Servers

- A **job** is a core component of large, distributed systems. It's a unit of work, it does some computational work that needs to be done in order for the app to actually have the proper functionality or data that it needs.
- They are mostly in charge of **Application Logic** - the context is not necessarily around the user like Business Logic. For example a job can be responsible for loading data from different sources into a database.

### Job Queues

- A way to know how the job servers know when to perform their work.
- A **queue** is FIFO (first-in-first-out). Whatever job comes first, it's going to be the job that goes to run.
- A **priority queue** is when things come with a certain priority and must be prioritized regardless.
- What gives the queue the actual jobs that are needed to be run? Web Servers send jobs into the Job Queue.

## 8. Services

- Web Servers communicate with services. Services encapsulate all of the system different components as a service.
- A service focus on one single responsibility:
  - Authentication
  - Newsfeed
  - Messaging
  - Communities
- _Microservices architecture:_ building your system as a composition of small services. A service is a self-contained deliverable system. It does one thing/domain really well. Other systems can utilize it and communicate with it for that intended purpose.

![Services](/assets/services.png)

## 9. Data

- Capture Client / Web Server behaviors (raw data), synthesize it so that better decisions can be made based on that data.
- **Data hose** takes raw data and massage it into a format that is more readable or in a format required by the Data Warehouse. It's the entry point for all the raw data of user action or behaviors or events firing in the system. It then modify the structure of the data.
- Raw + Massaged data is passed to the Data Warehouse. A **Data Warehouse** is a store solution.

## 10. Cloud Storage

- A backup source of storage that lives on the Cloud.
- You can backup any kind of file or data.

**CDN**: _Content Delivery Network_. It makes much more performant to deliver website's content to many different users all around the world.

- You can store the files that you receive from the website into the CDN either from Cloud Storage or from the Load Balancer.
- The CDN is going to hold the files you want to serve to the browser and don't have the browser go directly to the Web Server.
- **Edge Server**: servers that live in different parts of the world. They're localized servers that hold the same type of information that the browser asks for from the Origin Server.
- When a user makes a request, the DNS will work with the CDN to figure out the nearest Edge based on the user's location. Instead of going to the Origin Server, it goes to the nearest one.

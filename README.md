# Systems Design 101

A system mostly comprises of:

![Diagram](/assets/diagram.png)

## Browser / Client

- Allows you to interact with the web and access different websites / web apps.
- There are different browsers like Chrome, Safari and Firebox. A client can also be a mobile / watch app like the Facebook app.
- When you type the URL of the website you want to access in your browser, a **request** to a server is made for the files that builds up the website.
- When the server receives the request from the browser, it will send back a **response** with the appropriate JS, HTML and CSS files that your browser needs to use in order to build the website.
- This doesn't scale well when it comes to the billions of users in the world accessing the billions of websites. This is where you have to introduce some more pieces to the system.

## DNS

_Domain Name System_

- When you try to access a website you use a name that is human-readable. This domain name maps to an IP address (the computer recognizes it as a unique address somewhere on the Web).
- DNS helps figure out what specific **IP address** you need in order to access the website server so you can get the website's files.
- Many different companies that own a website have millions of IP addresses because there are numerous servers in the world that can house all the different files to help build up that website. A website doesn't specifically need one specific IP address.
- DNS has to maintain ipv4 and ipv6.

## Web Servers

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

## Load Balancer

Without a load balancer we have no real way to figure out how to properly manage the actual requests coming in.

- Its purpose is to _evenly_ distribute the load of requests between the servers available (assuming latency[^1] is always the same - in real life, different requests take different times).
- The load balancer acts as the _entry point_ into the system. We're not communicating directly to the server, instead we're going to communicate from the browser to the load balancer, and then the load balancer is going to distribute the requests.
- A very typical strategy for how to actually distribute the load is **Round Robin**. It goes in order on the list of servers. As requests come in, it goes to the next corresponding server in the list. We're, in theory, getting a balanced load because we're assuming the latency is the same between requests which means that if we evenly distribute these requests, all the servers should be sharing the load equally.
- Servers might go down. The load balancer is smart enough to know that if a server goes down, it's not going to route any request to it. Instead, it's going to distribute the load with the remaining servers that are up.
- The load balancer is the responsible for balancing the traffic that's coming in so your system is reliable. You want your servers to be evenly receiving the amount of requests so the servers are not overwhelmed.

[^1]: **Latency**: the time it takes for the browser to fire a request and then gets back a response from the server.

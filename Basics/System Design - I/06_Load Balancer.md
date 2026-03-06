# ⚖️ Load Balancer

## 🧐 The Problem with the Previous Design

So we saw in the previous video that we improved our service by scaling, right? That means we learned about horizontal scaling vs vertical scaling. So let's assume that we have done horizontal scaling.

But you might have noticed in our design that the users were directly connected to the web server.
```text
+-----------------------------------------+
|                  User                   |             www.mysite.com
|                                         | --------------------------------------------> +-------+
|   [Laptop]            [Mobile]          |                                               |       |
|  Web browser         Mobile app         | <-------------------------------------------- |  DNS  |
|                                         |               IP address                      |       |
+-----------------------------------------+                                               +-------+
          |                       |
          | www.mysite.com        | api.mysite.com
          |                       |
          |                       | 
          v                       v 
     (           directly connected          ) 
+-----------------------------------------+          read/write/update            +-----------------+
|                                         | ------------------------------------> |                 |
|               [Server]                  |                                       |       [DB]      |
|              Web server                 | <------------------------------------ |     Database    |
|                                         |             return data               |                 |
+-----------------------------------------+                                       +-----------------+

```
Our users from the web browser or mobile app are directly communicating, directly connected to the web server.

So what is the problem here?

Let's assume our server goes offline. Then it is a very big problem because the user is directly connected, and the one they are connected to goes offline, so our service is gone, and the users will be impacted.

Also, what other issues can arise? Let's assume many users are directly connecting to the server at the same time simultaneously. What problems can occur?

* **Web Server Load Limit:** The web server has its own limit, and let's assume its load limit is reached.
* **Slow Response:** Our server might start responding slowly.
* **Fail to Connect:** We might not be able to connect at all; our connection fails.

The best technique to handle these problems is a **Load Balancer**.

---

## 🦸‍♂️ Enter the Load Balancer

What does it do? As the name suggests, it balances the load. That means it evenly distributes the web traffic; whatever traffic is coming, it evenly distributes it.

If I explain the Load Balancer to you with a diagram, it looks something like this. Look carefully, this time the user is not directly connected to the server.

```text
                                +-------------+
                                |             |
                                |    User     |
                                |             |
                                +-------------+
                                       |
                                       | Public IP: 88.88.88.1
                                       v
                               +---------------+
                               |               |
                               | Load Balancer |
                               |               |
                               +---------------+
                                 /            \
              Private IP: 10.0.0.1\          / Private IP: 10.0.0.2
                                   v        v
                            +--------+    +--------+
                            |        |    |        |
                            | Server1|    | Server2|
                            |        |    |        |
                            +--------+    +--------+

```

```text
      +-------+                          +---------------------------------+
      | (DNS) | <----------------------- |              User               |
      +-------+                          |                                 |
          |                              |    [Laptop]         [Mobile]    |
          v                              |   Web browser      Mobile app   |
+---------------------------+            +---------------------------------+
| Domain        | IP Address|                              |
|---------------|-----------|                              | Public IP: 88.88.88.1
| mywebsite.com | 88.88.88.1|                              v
+---------------------------+                      +---------------+
                                                   | Load balancer |
                                                   +---------------+
                                                     /           \
                               Private IP: 10.0.0.1 /             \ Private IP: 10.0.0.2
                                                   v               v
                                           - - - - - - - - - - - - - - - - -
                                          |   +---------+       +---------+ |
                                          |   |         |       |         | |
                                          |   | Server1 |       | Server2 | |
                                          |   +---------+       +---------+ |
                                           - - - - - - - - - - - - - - - - -

```

We have done horizontal scaling; you can see we have added two servers, Server 1 and Server 2. But we are not connecting the users directly. We have placed a Load Balancer between the user and the servers.

What the Load Balancer does is evenly distribute whatever traffic there is. It sends some traffic to Server 1 and some traffic to Server 2.

### 🌐 Public IP vs Private IP

An important thing to note here is that when the user connects to the Load Balancer, they connect using the Load Balancer's **Public IP**. The Load Balancer then connects to the servers through a **Private IP**.

* **Private IP:** Private IPs are generally used for communication within the network between servers and are not accessible from anywhere outside.
* **Public IP:** Public IPs can be used by clients, like the users here, because it is accessible via Public IP.

---

## 😊 Why we are HAPPY now

So we just saw that it evenly distributes the web traffic. And as I mentioned, users connect to the load balancer via a Public IP, right? And the load balancer communicates with web servers via Private IP, which I told you a little while ago.

So now we have a Load Balancer, and we have taken two servers; we have done horizontal scaling and put a Load Balancer. So we are very happy.

**Benefits:**

* **No failover:** If Server 1 goes offline, Server 2 will get all the traffic. So the service won't go offline.
* **Improved Availability:** If a service fails, we will add a new healthy web server. This will help balance the load.
* **Handling Rapid Growth:** If our website is growing quite rapidly, let's assume the 2 servers are not enough to handle the traffic coming in. What will we do? We will add one more server. The Load Balancer will automatically start to send requests (evenly distribute the traffic) to the servers we have added.

The Load Balancer plays a very important role in our service.

---

## 😰 What's Next? The Database Problem

Now, what's next?

You might have noticed that our Database is still just one. It's still just one database.

What if the database goes down? Then it will be a very big problem. Okay, we added servers; we solved our tension of handling traffic, but the database is still the place from where we get the data. If, let's say, the database server goes down, then what will we do?

We will see how we solve this problem in the next.
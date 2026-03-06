# 🚀 Database & Multiple Servers

## 🔄 The Limitations of a Single Server

As we saw, a single server is not enough to handle the requests of multiple users or millions of users. If a lot of requests come in, and the number of users of our service increases, obviously one server will not be enough. We said we would look at how to scale our system next. We need to see how we will scale our single server system so it will be able to handle multiple users.

---

## ✂️ Decoupling: Separating Traffic and the Database

Let's do one thing. If you remember last time, we used only one server. Everything was happening on that same server; that server was handling the traffic and requests, and the database was also set up on the same server.

Now what we will do is keep a separate server to handle the traffic. We kept a separate server to handle the traffic, and we put the database on a different server.

* **Server-1:** Handles the **Traffic** (from web apps, mobile apps).
* **Server-2:** Hosts the **Database**.

What will happen because of this is that we will be able to scale both of them independently. Since both are separated, if I have to do something with the database, I can do it separately without any impact on the main server.

> **The Main Benefit:** The biggest benefit is that both can be scaled independently.

---

## 🏗️ The New Architecture Diagram

Let's look at this through a diagram. If you remember, we saw a diagram last time that did not have this database part; we only had just one server taking requests and sending responses.

But this time, we have separated them into two servers. One server is here, it will handle the traffic. And the second server is where we have kept the DB.

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
          v                       v
          ^                       ^
+-----------------------------------------+          read/write/update            +-----------------+
|                                         | ------------------------------------> |                 |
|               [Server]                  |                                       |       [DB]      |
|              Web server                 | <------------------------------------ |     Database    |
|                                         |             return data               |                 |
+-----------------------------------------+                                       +-----------------+

```

So whatever requests come, Server-1 will take the data from the database and put it out. Whatever needs to be sent to the user, we will send it. In this way, you can see we have formed two different servers. Why? We set up one server to handle the traffic, and we kept the second one to control the database.

What is the advantage of this? As I mentioned before, we can scale it separately.

* **Server-1** can be scaled separately.
* **Server-2** can be scaled separately.

So, our tension is over that all the load is not coming onto one server; we have set up a separate server to do the database work.

---

## 👀 What's Next?

Now, what do we have to study next?

* **Which Database to use?**
* We will study which database we will use for our service.
* There are many different types of databases today, and every service uses a different type of database depending on their usage.
* We will figure out what kind of data will be best for them and which type of database will be the most beneficial and scalable.
* We will discuss how to identify this because it is a very important interview question where they ask which database you would use for your service and why.


* **Scaling**
* Secondly, we always talk about scaling and making our service bigger.
* We will study what scaling actually is.
* We will read about the types: we can scale horizontally (**Horizontal Scaling**), and we can scale vertically (**Vertical Scaling**).
* We will read about both types, and both are very important questions from an interview point of view.
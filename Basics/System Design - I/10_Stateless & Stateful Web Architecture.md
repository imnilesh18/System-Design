# 🏛️ Stateless & Stateful Web Architecture

## 🤔 What is Session Data / State?

Now we will learn about Stateless and Stateful Web Architecture. The name sounds quite difficult, but it's nothing; it's a very simple thing.

Before reading this, let's know one thing: what is the session or state of a user?

> **Definition:** Session Data or State is data that represents a user's session on the web. The information related to the user is called state or session data.

What can it help us with?

* It can help identify a user.
* It can authenticate the user.

Keep this in mind; it will make understanding the rest easier.

---

## 😫 The Problem with Stateful Architecture

Let's assume there is User A and User B.

```text
user-A                                     web-server-1
 ( )     --------------------------------> [========] state info' of user-A
 \_/       \
            \
             \ /
              X 
             / \                           web Server-2
                \
user-B           ------------------------> [========] state-info of user-B
 ( )     --------------------------------> 
 \_/

```

* User A sends a request to Web Server 1.
* User B sends a request to Web Server 2.

Web Server 1 has the state information for User A (e.g., profile image, login session info). Web Server 2 has the state information for User B.

**The Issue:**
Pay attention: What if User A's request goes to Web Server 2?

If it goes to Web Server 2, the authentication will fail! Because Web Server 2 doesn't have User A's information, Web Server 2 will think this is an unauthenticated user, which is a mess. This is a problem.

**The Restriction:**
Let's look at another diagram with Server 1, Server 2, and Server 3. Server 1 has User A's data, Server 2 has User B's, and Server 3 has User C's.

```text
     [Laptop]                   [Laptop]                   [Laptop]
      User A                     User B                     User C
        |                          |                          |
        | http request             | http request             | http request
        |                          |                          |
        v                          v                          v
+----------------+         +----------------+         +----------------+
|                |         |                |         |                |
|    Server 1    |         |    Server 2    |         |    Server 3    |
|                |         |                |         |                |
|     +----+     |         |     +----+     |         |     +----+     |
|     | -- |     |         |     | -- |     |         |     | -- |     |
|     | -- |     |         |     | -- |     |         |     | -- |     |
|     +----+     |         |     +----+     |         |     +----+     |
|                |         |                |         |                |
| • Session data |         | • Session data |         | • Session data |
|   for User A   |         |   for User B   |         |   for User C   |
|                |         |                |         |                |
| • Profile      |         | • Profile      |         | • Profile      |
|   image for    |         |   image for    |         |   image for    |
|   User A       |         |   User B       |         |   User C       |
|                |         |                |         |                |
+----------------+         +----------------+         +----------------+

```

Because of this, User A's HTTP request *must* be redirected to Server 1 only. If it goes anywhere else, authentication will fail because the other servers cannot identify the user.

> **The Overhead:** We have a tension now: a user's request *will have to go* to a specific server only. While possible using "sticky sessions" in Load Balancers, it is a problem and creates overhead.

**Scaling Difficulties:**
Imagine traffic is low, and we want to remove a server. It's not easy! If you remove Server 1, User A's information is gone. You'd have to figure out how to move that information to Server 2 or 3. It's quite difficult. What if a server fails? User A's data is lost.

In such cases, a **Stateless Architecture** will help us.

---

## ⚖️ Stateless vs. Stateful Web

Let's look at the differences.

| Stateless Web | Stateful Web |
| --- | --- |
| Does **not remember** client data. | **Remembers** client data. |
| Does **not store** state information. | **Stores** state information. |
| Simple, robust. | Overhead, not simple. |

In a stateful web, all state information is stored on the server, creating overhead, tension, and making scaling/failover very difficult.

---

## 💡 The Solution: Stateless Architecture

This diagram will clearly show what a stateless architecture looks like.

```text
   [Laptop]                 [Laptop]                 [Laptop]
    User A                   User B                   User C
       \                        |                        /
        \ http request          | http request          / http request
         \                      |                      /
          v                     v                     v
        - - - - - - - - - - - - - - - - - - - - - - - - -
       |                                                 |
       |                  +----+  +----+                 |
       |                  |    |  |    |                 |
       |               +----+  +----+  +----+            |
       |               |    |  |    |  |    |            |
       |               +----+  +----+  +----+            |
       |                                                 |
       |                   Web servers                   |
        - - - - - - - - - - - - - - - - - - - - - - - - - 
                                |
                                | fetch state
                                v
                            +-------+
                            |       |
                            |  { }  |
                            |       |
                            +-------+
                         Shared Storage

```

1. **Any Server Can Handle Any Request:** Now, an HTTP request from any user can go to *any* web server. There is no tension!
2. **Shared Storage:** All the state information has been moved out of the web servers and put into a separate, shared data store (Shared Storage).
3. **Fetching Data:** Whichever web server receives the request will simply fetch the state information/data from the shared storage.

> **Result:** Look how simple it has become, and our tension is gone! The problems we faced in stateful architecture are solved.

---

## 🌟 Our Amazing System Design

Now, our service design looks AMAZING! We have made a lot of improvements from the start. Let's recap what we've added:

* ✔️ Multiple Servers
* ✔️ Load Balancer
* ✔️ Master-Slave DB
* ✔️ Cache DB
* ✔️ CDN
* ✔️ **Stateless Web** (Shared storage for state data)

### 🗺️ The Complete Architecture Diagram

Let's look at our entire service again through a diagram. It's looking quite perfect now.

```text
+-------+                     +---------------------------------+                   +-------+
|       | <------------------ |              User               | ----------------> |       |
|  DNS  |                     |    [Laptop]         [Mobile]    |                   |  CDN  |
|       | ------------------> |   Web browser      Mobile app   |                   |       |
+-------+                     +---------------------------------+                   +-------+
                                        |             |
                         www.mysite.com |             | api.mysite.com
                                        v             v
                                    +---------------------+
                                    |    Load balancer    |
                                    +---------------------+
                                           /       \
                                          v         v
                             - - - - - - - - - - - - - - - - - - - - - -
                            |             Web tier                      |
                            |  +-------+  +-------+  +-------+  +-------+ | (1) Auto scale
                            |  |Server1|  |Server2|  |Server3|  |Server4| |
                            |  +-------+  +-------+  +-------+  +-------+ |
                             - - - - - - - - - - - - - - - - - - - - - -
                                 :        |        :         \          \
                                 :        |        :          \          \ Session data
                                 v        v        v           v          v 
           - - - - - - - - - - - - - - - - - - - - - - - - -   +-------+  +-------+
          |                                                 |  | CACHE |  |   {}  |
          |  +--------+  Replicate  +---------+  Replicate  |  |       |  | NoSQL |
          |  |Slave DB| <========== |Master DB| ==========> |  +-------+  +-------+
          |  +--------+             +---------+  +--------+ |    Cache      
          |                                      |Slave DB| |
          | Data tier                            +--------+ |
           - - - - - - - - - - - - - - - - - - - - - - - - -

```

1. User gets the Load Balancer's IP address from DNS.
2. Static content is fetched from the CDN.
3. The Load Balancer evenly distributes requests among the web servers (Server 1, 2, 3, 4).
4. If data is cached, it's retrieved from the Cache.
5. If not, read requests go to the Slave DB, thanks to our Master-Slave DB model.
6. **NEW:** If we need session data, the web servers fetch it from a separate NoSQL database that we have kept just for session data.

*Note: You can use any database for the shared storage depending on your usage (Relational DB, Cache DB, NoSQL, etc.). The main thing is that we separated the session data storage from the web servers.*

**The Power of Auto-Scaling:**
Since we have separated the session data into a separate DB, we can now easily do **Auto-scaling**. We can comfortably add or remove servers according to the load because no server holds unique user state.

Overall, our service has become quite strong and powerful now!
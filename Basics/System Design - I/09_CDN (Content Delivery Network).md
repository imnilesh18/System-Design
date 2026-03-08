# 🌍 CDN (Content Delivery Network)

## 🤔 What more can we improve?

So we saw last time how we reduced the response time and load on our database using Caching. Because of that, our overall response time had become quite good.

So, further, what else is there to improve our service? We will learn about **CDN**. You must have heard this term; its full form is **Content Delivery Network**. We will understand what it is and how it helps improve our service.

But before knowing that, we need to know two things. A website has two types of content:

1. **Static Content**
2. **Dynamic Content**

### 🖼️ Static vs. Dynamic Content

* **Static Content:** These are things that do not change. For example, images, videos, CSS files, JavaScript files. A profile image that won't change.
* **Dynamic Content:** This changes according to the user input. For example, if you have a website that sells books, if a book has been published, that book won't change, so the e-book is static content. But the user's shopping cart or profile is dynamic.

---

## 🦸‍♂️ How a CDN Works

What a CDN does is it keeps the static content and provides it to the website so we get the static content immediately from the CDN. This removes the tension from the web server of storing and sending static content.

Let's see how it works from a high-level view.

Let's assume a user visits a website. The CDN server closest to the user will provide all the static content.

> **Important Note:** Remember that the farther the server is from you, the longer your website will take to load.

For example, if a website's CDN server is in the US, and I access it from India, and someone sitting in the US accesses it, obviously their website will load much faster because the CDN server is close to them.

### 🔄 The CDN Request Flow

Let's understand this with a diagram.

```text
                         static
                         content
               --------------------->               --------------------->
               <---------------------               <---------------------
    +--------+                        +----------+                         +---------------+
    |        |                        |          |                         |               |
    |  User  |                        |   CDN    |                         |    origin     |
    | [Laptop]                        |  server  |                         |    server     |
    |        |                        |          |                         |               |
    +--------+                        +----------+                         +---------------+
                         dynamic
                         content
               - - - - - - - - - - - - - - - - - - - - - - - - - - - - - >
               <- - - - - - - - - - - - - - - - - - - - - - - - - - - - -

```

1. Let's assume User A needs an image. They make a request.
2. The request goes to the CDN.
3. If the CDN does NOT have that image, it will request it from the Web Server (`image.png`).
4. The server sends the response (the image) to the CDN.
5. The CDN stores it for some time.
6. Then the CDN sends the response to User A.

Now let's assume the image stored in the CDN hasn't expired or been deleted yet. Meanwhile, **User B** requests the same image. There's no need to go to the web server; the CDN will directly send the response to User B, making it very fast. User B is happy!

```text
 +--------+        1. get image.png          
 |        | ---------------------------->  
 | User A |                                   +-----+  2. if not in CDN, get image.png from server   +--------+
 |        | <----------------------------     |     | - - - - - - - - - - - - - - - - - - - - - - -> |        |
 +--------+        4. return image.png        |     |                                                |        |
                                              | CDN | <- - - - - - - - - - - - - - - - - - - - - - - | Server |
 +--------+        5. get image.png           |     |  3. store image.png in CDN                     |        |
 |        | ---------------------------->     |     |                                                +--------+
 | User B |                                   +-----+ 
 |        | <----------------------------       
 +--------+        6. return image.png       

```

---

## 🤔 Things to Consider When Using a CDN

Now let's see what things we have to consider to use a CDN.

1. **Cost:** Third-party CDN vendors charge money for every data transfer. Obviously, you shouldn't put things in the CDN that are rarely used to avoid wasting money.
2. **Expiry Time:** Just like in caching, there should be an expiry time set for when data expires so it can be removed from the CDN.
* **Not too low:** The CDN will repeatedly request the web server, increasing the load.
* **Not too high:** The data might become stale.


3. **CDN Fallback/Backup:** If the CDN goes down, we must have a backup mechanism to fetch data directly from the web server.
4. **Invalidating Files:** We need a way to invalidate a file (remove it from the CDN) before it naturally expires.
* **API:** CDN vendors provide APIs to invalidate files.
* **Versioning:** You can add a version parameter to the URL (e.g., `image.png?v=2`). The CDN understands we need version 2, meaning version 1 is invalidated.



---

## 🗺️ The Ultimate Architecture

Look at how much benefit we got from adding a CDN. Let's look at our entire service's full design after adding the CDN.

```text
 +-------+                     +---------------------------------+                   +-------+
 |       | <------------------ |              User               | ----------------> |  (1)  |
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
                              - - - - - - - - - - - - - - - - - - 
                             |             Web tier              |
                             |   +---------+         +---------+ |
                             |   | Server 1|         | Server 2| |
                             |   +---------+         +---------+ |
                              - - - - - - - - - - - - - - - - - - 
                                |   :     |   \   /   |     :   |
                                |   :     |    \ /    |     :   |
                                |   :     |     X     |     :   +---------> +---------+
                                |   :     |    / \    |     :  < - - - - -  |         |
                                |   :     |   /   \   |     :               |  CACHE  |
                                |   :     +--/-----\--|-----:-------------> |   (2)   |
                                |   :       /       \ |     :  < - - - - -  |         |
                                |   :      /         \|     :               +---------+
                                v   v     v           v     v
                  - - - - - - - - - - - - - - - - - - - - - - - - - - - 
                 |                                                     |
                 |      +-----------+                 +-----------+    |
                 |      |           |    Replicate    |           |    |
                 |      | Master DB | <=============> | Slave DB  |    |
                 |      |           |                 |           |    |
                 |      +-----------+                 +-----------+    |
                 | Data tier                                           |
                  - - - - - - - - - - - - - - - - - - - - - - - - - - - 

```

1. **DNS & Routing:** The user gets the Load Balancer's IP address from the DNS. The user connects to the Load Balancer.
2. **Static Content (CDN):** The user gets all static content directly from the CDN immediately.
3. **Dynamic Content (Web Tier):** For dynamic content, the Load Balancer distributes requests to the Web Server layer (Server 1, Server 2).
4. **Data Tier (Caching & DB):** The Web Server checks the Cache first. If the response is in the Cache, it won't even go to the DB. If it goes to the DB, we have a Master-Slave DB model for fast and reliable data handling.

Overall, by using a Load Balancer, a CDN, multiple servers, a Master-Slave DB model, and a Cache, look how good, fast, and reliable our system has become!

> **Interview Tip:** This is how we slowly progress and improve our service in interviews. You start with a simple system, the interviewer gives you a situation (e.g., "traffic increases", "DB fails"), and you gradually introduce solutions like Load Balancers, DB Replication, Caching, and CDNs to improve the system.
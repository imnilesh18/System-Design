# ⚡ Caching

## 🚀 The Quest for Faster Response Times

So previously, we saw that we had improved our service quite a bit. We fixed the tension of our database going down by using Master-Slave DB Replication. Then, to handle traffic, we used a Load Balancer and multiple servers.

At the end, I asked: Can we improve even more? Can we improve the load/response time of our service?

The answer is **YES**. We will use a **CACHE**.

---

## 🦸‍♂️ Cache to the Rescue

A cache is actually a type of temporary storage.

What does it store?

1. **Results of expensive responses:** Let's assume there are some requests whose responses take a very long time to compute (expensive responses). So that we don't have to compute them repeatedly, we will store them in the cache. When a request comes repeatedly for the same thing, it won't have to calculate it again; it will just return the response to the user.
2. **Frequently accessed data:** There is some data that we try to access very frequently. Why should we bother the database repeatedly? We can just store it in the cache and return it from there.

> **The Result:** We can serve requests more quickly!

Our service has become quite fast. We put a cache in the middle, and it will serve our requests, so we will be going very fast. And the database will be bothered less. We will do some caching and lighten the database's workload.

---

## 📖 The Read-Through Cache Strategy

We said we are going to take the help of a cache. So what did we do? We had a Web Server, and here was a DB. We put a Cache in between them.

**How it works (Read-Through Cache):**

```text
              1. If data exists in cache, read data from cache
+----------+ <---------------------------------------------------- +-------+
|          |                                                       |       |
|   Web    |                                                       |       |
|  server  |  2.2 Return data to the web server                    | Cache |  2.1 If data doesn't exist in cache,   +----------+
|          | <---------------------------------------------------- |       | <------------------------------------- |    DB    |
+----------+                                                       +-------+         save data to cache             | Database |
                                                                                                                    +----------+

```

1. If the web server needs the response for a request, it will first check if the Cache has it or not.
2. **Cache Hit:** If the Cache has it, it will return the response.
3. **Cache Miss:** If it doesn't have it, it will extract the result from the Database, store it in the Cache (so it can be used next time), and then return the response.

This process is called a **Read-Through Cache**.

---

## 🛠️ Interacting with the Cache

How do we actually do this? It's quite a simple way to interact with a cache. Cache servers provide APIs. You can directly call those APIs to interact with the cache.

**Example:**
Let's look at a small example. Let's assume I need the value for `my-key`. We extracted it from the database and stored it in the cache, saying the value of `my-key` is `Hello`.

```java
Cache.set('my-key', 'Hello', 3600 seconds);
Cache.get('my-key');

```

I also told the cache it will stay there for 3600 seconds. The next time a request comes, we will ask the cache, and it will send the response `Hello` to the user.

---

## 🤔 Things to Consider When Using a Cache

Moving forward, what things do we have to consider to use a cache?

* **Data Characteristics:** A cache is very useful when data is read frequently but modified infrequently. Because if you modify the database, you have to reflect the same things in the cache too.
* **Expiration Policy:** If data is in the cache for a long time, we must decide on its expiration policy.
* **Not too low:** If it expires too quickly (e.g., after 1 second), you will keep bothering the database to reload data.
* **Not too high:** If it stays for too long, the data might become stale. For example, if I stored data today, the database is modified tonight, and the expiration is one week, my data is stale for a week.


* **Consistency:** The data between the DB and the cache must be consistent. The changes made in the DB must be reflected in the cache.
* **Avoiding SPOF:** We should use multiple cache servers across different data centers. If all web servers depend on one cache server, and that server goes down, everyone will face a problem. This is called a Single Point Of Failure (SPOF).
* **Eviction Policy:** When the cache memory gets full, we obviously want to remove the old data. This is called eviction. You can use any policy according to your usage:
* **LRU (Least Recently Used):** Remove data that hasn't been used for a long time.
* **LFU (Least Frequently Used):** Remove data that is used very rarely.
* **FIFO (First In First Out)**



These are all the things we should consider for using a cache server.

---

## ⏭️ Next Steps

With this, there has been quite an improvement, right? Next, we will see if there is anything else we can do to improve the performance of our service.
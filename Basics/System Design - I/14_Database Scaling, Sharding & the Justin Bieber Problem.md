# 🗄️ Database Scaling, Sharding & the Justin Bieber Problem

## 📈 The Database Bottleneck
Very important and interesting topic: Database Scaling, Sharding, and the Justin Bieber Problem.

If our service has grown quite a lot, many users are using it, and traffic has increased significantly, then obviously data will grow as well. As the data grows, your database will get overloaded.

```text
Data grows [Up arrow]  =>  Database overloaded :(  =>  Scale your Database Servers

```

Now it is the right time to use scaling for our database servers. There are two ways to scale a database server, which we have studied before:

1. **Vertical Scaling**
2. **Horizontal Scaling (Sharding)**

```text
       Vertical Scaling
(Increase CPU, RAM, DISK, etc)

          +----------+      ^
          |   __     |      |
          |          |      |                 Horizontal Scaling
          |          |      |                 (Add more servers)
          |          |      |
          |   __     |      |
          +----------+      |
                            |          +----+  +----+  +----+  +----+
          +--------+        |          | __ |  | __ |  | __ |  | __ |
          |   __   |        |    VS    |    |  |    |  |    |  |    |
          |        |        |          | __ |  | __ |  | __ |  | __ |
          |   __   |        |          +----+  +----+  +----+  +----+
          +--------+        |
                            |          +----+  +----+  +----+  +----+
          +----+            |          | __ |  | __ |  | __ |  | __ |
          | __ |            |          |    |  |    |  |    |  |    |
          | __ |            |          | __ |  | __ |  | __ |  | __ |
          +----+            |          +----+  +----+  +----+  +----+
                            |
                             ---------------------------------------->

```
---

## ⬆️ Vertical Scaling (The "Scale Up" Approach)

In vertical scaling, you give more power to your existing machine. You increase its CPU, RAM, or Disk space.

* **Example:** According to AWS RDS (Amazon Relational DB Service), you can increase your RAM up to 24 TB!
* **Real-World Case:** It's an interesting fact that in 2013, StackOverflow had 10 million unique visitors every day, and they managed it with just *one* master database.

**Drawbacks of Vertical Scaling:**

1. **Hardware Limits:** There is obviously a hardware limit. How much power or RAM can you add? There's a limit you can't cross.
2. **Single Point of Failure (SPOF):** Just like StackOverflow had only one master DB. If that single thing fails, everything ends; you'd leave a terrible impression on your millions of visitors.
3. **Costly:** Adding lots of RAM and memory is very costly.

---

## ➡️ Horizontal Scaling / Sharding (The "Scale Out" Approach)

Horizontal scaling is also called **Sharding**. In this, we add more servers. Instead of making one database large, we divide the data into smaller chunks called "shards".

```text
Horizontal Scaling :- Sharding (Adding more servers)

- [ Large DB ]   --->   [shard - 1]   [shard - 2]
                        [shard - 3]   [shard - 4]

                        shards
                        smaller &
                        easy management

```

Because shards are small, managing them becomes quite easy.

### 🔍 How Sharding Works: The Hash Function

Let's look deeper into horizontal scaling. Let's assume we have 4 shards (0, 1, 2, 3). How do we decide which data goes to which shard? We create a **Hash Function**.

In the hash function, we choose a key called the **Sharding Key**. The sharding key is usually a column from the database table. You must choose the sharding key very carefully.

* **Example:** Let's say we choose `user_id` as the sharding key.
* **The Logic:** If we have 4 shards, we do `user_id % 4`.
```text
                         SHARDING KEY
                               |
                               v
                          /----------\
                         < user_id % 4 >
                          \----------/
                               |
            ---------------------------------------
            |          |               |          |
            v          v               v          v
         +----+      +----+         +----+      +----+
         |    |      |    |         |    |      |    |
         | 0  |      | 1  |         | 2  |      | 3  |
         |    |      |    |         |    |      |    |
         +----+      +----+         +----+      +----+

```
* If the result is `0`, data goes to Shard 0.
* If the result is `1`, data goes to Shard 1.
* If the result is `2`, data goes to Shard 2.
* If the result is `3`, data goes to Shard 3.



> **CRITICAL RULE:** It is very important to choose your sharding key wisely for the **even distribution of data**. If it's not evenly distributed, you might end up with 60% of data on Shard 0, 10% on Shard 1, 30% on Shard 2, and nothing on Shard 3.

If we zoom into each shard, you'll notice that the **schema is the same** for every table, but the **data inside is different**.

```text
            Shard 0                              Shard 1
    +---------------------+              +---------------------+
    |        Users        |              |        Users        |
    +-----------+---------+              +-----------+---------+
    |  user_id  |   ...   |              |  user_id  |   ...   |
    +-----------+---------+              +-----------+---------+
    |     0     |         |              |     1     |         |
    |     4     |         |              |     5     |         |
    |     8     |         |              |     9     |         |
    |    12     |         |              |    13     |         |
    |    ...    |         |              |    ...    |         |
    +-----------+---------+              +-----------+---------+

         Schema same है। But data सभी में different है।

            Shard 2                              Shard 3
    +---------------------+              +---------------------+
    |        Users        |              |        Users        |
    +-----------+---------+              +-----------+---------+
    |  user_id  |   ...   |              |  user_id  |   ...   |
    +-----------+---------+              +-----------+---------+
    |     2     |         |              |     3     |         |
    |     6     |         |              |     7     |         |
    |    10     |         |              |    11     |         |
    |    14     |         |              |    15     |         |
    |    ...    |         |              |    ...    |         |
    +-----------+---------+              +-----------+---------+

```

---

## ⚠️ Drawbacks of Sharding

Sharding is great, but it has drawbacks.

### 1. Resharding

What happens if a shard reaches a stage where it can no longer hold more data (shard exhaustion)? This can happen due to uneven distribution.

If a shard is full, you obviously have to change your hash function and your sharding key. Then you'll have to redistribute all the data again across all shards. This is a huge overhead and takes a lot of effort.

### 2. The Celebrity Problem (Hotspot Key Problem)

This is when excessive access to a particular shard causes a shard overload.

```text
      SHARD-1                          SHARD-2
 +---------------+                 +-------------+
 | Justin Bieber | <--- Read       | No one      |
 | eminem        | <--- Read       | loves       |
 | Lady Gaga     | <--- Read       | me   :(     |
 |      :(       | <--- Read       |             |
 +---------------+                 +-------------+
   OVERLOADED 

```

For example, let's say Shard-1 holds data for Justin Bieber, Eminem, and Lady Gaga. Because they are so popular, maximum read requests will go to Shard-1, overloading it, while Shard-2 might get zero requests.

**The Real Justin Bieber Problem:**
This is actually a famous problem. Years ago, before Facebook acquired Instagram, Instagram noticed that whenever Justin Bieber posted a photo, the app would become extremely slow. This was because his millions of fans would start liking and commenting immediately, causing a massive spike in read and write operations on the database. Caching didn't help because the updates were too frequent. Eventually, they had to implement a completely separate database cell just to handle his "like" counters!

**The Fix:** To fix this hotspot issue, you can assign one celebrity to each shard. Put Justin in Shard-1, Eminem in Shard-2, and Gaga in Shard-3 so the load is distributed.

### 3. Join Operations

Because we have sharded the database, it becomes very difficult to perform JOIN operations using SQL across different tables, since the data is now spread across multiple servers. A common fix is to denormalize the data and put it all into one large table so you can query a single table.

---

## 🏆 The Ultimate System Summary
```text
      +-----+                 +-----------------+                 +-----+
      | DNS | <-------------> |      User       | --------------> | CDN |
      +-----+                 |                 |                 +-----+
                              |  [Web browser]  |
                              |  [Mobile app]   |
                              +-----------------+
                                 |           |
                  www.mysite.com |           | api.mysite.com
                                 v           v
                              +-----------------+
                              |  Load balancer  |
                              +-----------------+
                                       |
 - - - - - - - - - - - - - - - - - - - | - - - - - - - - - - - - - - - - - - -
| DC1                                  v                                      |
|                               +-------------+                               |
|                               |             |        +---------------+      |
|                               | Web servers | -----> | [✉] [✉] [✉] |      |
|                               |             |        | Message Queue |      |
|                               +-------------+        +---------------+      |
|                                 /   |   \                    |              |
|                                /    |    \                   v              |
|                               /     |     \           +-------------+       |
|                              v      v      \          |             |       |
|       +-------++-------++-------+ +-------+ \         |   Workers   |       |
|       |       ||       ||       | | CACHE |  \        |             |       |
|       |Shard 1||Shard 2||Shard..| | CACHE |   \       +-------------+       |
|       |       ||       ||       | | CACHE |    \                            |
|       +-------++-------++-------+ +-------+     \                           |
|           (1) Databases             Caches       \                          |
 - - - - - - - - - - - - - - - - - - - - - - - - - -\- - - - - - - - - - - - -
                                                     \         +-------+
                                                      +------> |  { }  |
                                                               | NoSQL |
                                                               +-------+
                                                                  (2)
                                       ^
                                       |
                         +---------------------------+
                         |  +---------+ +---------+  |
                         |  | Logging | | Metrics |  |
                         |  +---------+ +---------+  | <---- Tools
                         |  +----------++----------+ |
                         |  |Monitoring||Automation| |
                         |  +----------++----------+ |
                         +---------------------------+

```

We have reached a major milestone! Let's summarize everything we've learned to build a strong, reliable, and highly scalable service.

```text
1. Keep web tier stateless.
2. Build redundancy at every tier (Master-Slave DB replication).
3. Cache data as much as you can.
4. Support multiple data centers.
5. Host static assets in CDN.
6. Scale your data tier by sharding.
7. Split tiers into individual services (using Message Queues).
8. Monitor your system and use automation tools.

```

Our service now looks very good and perfect. Your foundation is now strong!
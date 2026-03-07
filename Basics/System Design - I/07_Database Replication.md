# 🗄️ Database Replication

## 😰 The Problem: What if the Database Fails?

So previously, we saw that we had solved the tension of handling traffic by using multiple servers. But then at the end, I asked: what if our database fails? What if the database server goes down? What will we do then?

In such a case, a very popular technique is used: **Database Replication**.

## 🦸‍♂️ Enter Database Replication

Let's look at Database Replication. As the name suggests, we make copies of the database; we make its replicas.

> **Note:** This is a very important topic, guys. It is heavily discussed in interviews.

In a database, we need to look at two things:

1. **Master** - The original database
2. **Slave** - The copy (replica)

The main one is our master database, our original database. And the copies we make, we call them slave databases or replicas.

### ⚙️ How it Works: Master vs Slave

| Master DB | Slave DB |
| --- | --- |
| Only **write** operations. | **Read** operations. |
| Handles data-modifying commands (insert, delete, update). | Gets copies of data from the master. |

**The Master Node:**
In the master, mainly write operations happen. Whatever insert, delete, and update requests are coming in, the data-modifying commands, they all happen in the master.

**The Slave Nodes:**
Whatever read operations or read requests are coming in, they are all happening in the slave.

**The Replication Process:**
An important thing is that whatever updates are happening in the master DB, they are all continually being updated in the slave DBs as well. Whatever changes are coming in the master DB, they are being updated in the slave DBs. This replication is always running, processing is always going on. Obviously, we don't want our slave to be outdated; we want both master and slave to be updated so the data remains exactly the same in both.

---

## 🏗️ Visualizing the Architecture

To understand this better, let's look at a diagram showing the master-slave architecture.

```text
                             +-------------+
                             | Web servers |
                             +-------------+
                               |         |
                        writes |         |
                               v         |
                         +-----------+   |
                         |    DB     |   |
                         | Master DB |   |
                         +-----------+   |
                               |         |
                DB replication |         |             reads
                               +-------> +-----------+ <---------+
                               |         |    DB     |           |
                               |         | Slave DB1 |           |
                               |         +-----------+           |
                               |                                 |
                DB replication |                       reads     |
                               +-------> +-----------+ <---------+
                               |         |    DB     |           |
                               |         | Slave DB2 |           |
                               |         +-----------+           |
                               |                                 |
                DB replication |                       reads     |
                               +-------> +-----------+ <---------+
                                         |    DB     |
                                         | Slave DB3 |
                                         +-----------+

```

Look here, there is a web server at the top. And below, we have our master DB and slave DBs. Pay attention: whatever write requests are coming from the web server, they are going to the master DB. And whatever updates are happening in the master DB, they are replicating to the slave DBs (Slave DB1, Slave DB2, Slave DB3).

Whatever read requests are coming, they are all going to your slave DBs. Look closely on the right side. By looking at this, you can tell that you can distribute all the requests in parallel. Because it is distributing the requests, the overall performance and response of our system will also be good.

### ⚖️ The Ratio: Why more Slaves?

Before we look at the advantages, let me tell you one thing: in mostly all applications in real-life, the ratio of read requests to write requests is quite high.

Therefore, the number of slaves is generally always greater than the number of masters.
`# slaves > # masters`

---

## ✨ Advantages of DB Replication

So what are the advantages of DB replication?

1. **Better Performance:** In the master-slave model, all write operations and updates happen on the master nodes. And all read operations are distributed among the slave nodes. This improves performance because it distributes the load across multiple processes in parallel.
2. **Reliability:** As the name suggests, you can rely on your service. If one server is destroyed by anything, if there is any issue in it, your data is still preserved. You don't need to worry about data loss because your data is replicated across multiple locations.
3. **High Availability:** Since you have replicated the database in different locations, your service will remain operational. Even if a database is offline, you can access data stored in another database.

---

## 🛠️ Handling Failures

So if I ask you, what will you do if a database goes offline? Database replication is the best thing to handle this.

* **If a SLAVE DB goes offline:** If even one slave database goes offline, we will direct the read operations directly to the master DB. Let's assume there is a problem with a slave DB, so start handling all read operations from the master DB temporarily. Meanwhile, we will add a new slave.
* **If the MASTER DB goes offline:** Let's assume the master DB itself goes offline, there is some problem in it. In that case, what will we do? We will pick up a slave DB and promote it, turning it into the master DB. Until the write operations are running temporarily on this new master DB, we will bring in a new slave DB to replace it.

> **Real-World Complexity:** What happens in real-life production systems is that promoting a new master is very complicated. Because there can be a case where the slave DB might not be fully updated with the data, maybe half the changes haven't reached it. There are many ways to tackle this, many recovery scripts, but they are out of scope for now because we are just forming our foundation. We will learn more about this further down the line.

---

## 🗺️ The Big Picture

Let's see how our overall system looks now. We have improved a lot. Let's look from the start:

```text
+-------------------------------------------------+
|                      User                       |             www.mysite.com            +-------+
|                                                 | ------------------------------------> |       |
|      [Laptop]                [Mobile]           |                                       |  DNS  |
|     Web browser             Mobile app          | <------------------------------------ |       |
|                                                 |               IP address              +-------+
+-------------------------------------------------+
               |                       |
www.mysite.com |                       | api.mysite.com
               v                       v
             +---------------------------+
             |       Load balancer       |
             +---------------------------+
                     /           \
                    v             v
         - - - - - - - - - - - - - - - - - - -
        |             Web tier                |
        |     +---------+         +---------+ |
        |     |         |         |         | |
        |     | Server 1|         | Server 2| |
        |     |         |         |         | |
        |     +---------+         +---------+ |
         - - - - - - - - - - - - - - - - - - -
               |       \           /       |
         Write |  Read  \         /  Write | Read
               |         \       /         |
               v          v     v          v
         - - - - - - - - - - - - - - - - - - -
        |             Data tier               |
        |     +---------+         +---------+ |
        |     |         |Replicate|         | |
        |     |Master DB|-------->| Slave DB| |
        |     |         |         |         | |
        |     +---------+         +---------+ |
         - - - - - - - - - - - - - - - - - - -

```

1. The user gets the IP address from the DNS.
2. Using that IP address, the user connects to the Load Balancer.
3. The HTTP request is then routed to either Server 1 or Server 2 (Web Tier).
4. The web server then decides: if it needs to make a read request, it will do it from a Slave DB. If a write request comes, it will make it to the Master DB.
5. In the meantime, whatever updates are happening in the Master DB are continually being replicated to the Slave DB (Data Tier).

Overall, you can see our system has become quite solid. We have handled the web layer (the traffic tension) by adding multiple servers and a load balancer. And another tension we had, data loss, we have also solved that through database replication.

---

## 🏎️ What's Next?

So now we will see that some websites are very fast; their response time is quite good. So what is the way we can further improve the response time of our service?
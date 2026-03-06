# ⚖️ Vertical Scaling vs Horizontal Scaling

## 🚀 The Need to Scale

Today we will see what scaling is. Let's assume that your service has become very popular. It will be used by millions of users. So obviously, to handle the requests of so many users, your service must have the ability and capacity. So what will we do? We will try to make our service bigger, we will try to scale it.

There are two types of scaling:

1. **Vertical Scaling**
2. **Horizontal Scaling**

What do these mean? Let's see.

---

## ⬆️ Vertical Scaling (Scale-Up)

Vertical scaling is also called "**scale-up**". What actually happens in vertical scaling is that you add more power to the server you currently have. You increase its capacity.

> **Definition:** It means adding more power to your servers, for example, by adding more CPU or adding more RAM/Memory to your service.

You are increasing the power of the server you already have. This is called vertical scaling.

---

## ➡️ Horizontal Scaling (Scale-Out)

Horizontal scaling is also called "**scale-out**". In horizontal scaling, we add more servers. Instead of increasing the power in just one server, we add more servers of the same type.

---

Let's look at a small picture through which it will become quite clear.

```text
           +-----------+
       ^   |           |
       |   |           |             +-------+   +-------+   +-------+
       |   |           |             |       |   |       |   |       |
       |   | +-------+ |             |       |   |       |   |       |
       |   | |       | |             +-------+   +-------+   +-------+
           | |       | |
           | +-------+ |              ------------------------------->
           |           |
           +-----------+

         Vertical Scaling                   Horizontal Scaling
           (Scaling up)                       (Scaling out)

```

* **Left Side (Vertical Scaling):** You are increasing the power of the same server. You have increased its CPU, increased its RAM.
* **Right Side (Horizontal Scaling):** You have replaced the single server and added multiple other servers.

---

## 🤔 When to use which?

Then the question arises: When to use Vertical and when to use Horizontal scaling?

| Vertical Scaling | Horizontal Scaling |
| --- | --- |
| Use when traffic is **LOW**. | Preferred for **large scale applications**. |
| **Simplicity** is its advantage. | Used for **Huge traffic**. |

**Why Vertical for Low Traffic?**
Vertical scaling should only be done when traffic is very low. Because if a lot of traffic comes, how much load, how much power can you give to a single server? There will be a limit, right? So vertical scaling is suitable only when the traffic is quite low.

**The Advantage of Vertical:**
It is an obvious thing, if you have only one server and you are scaling that one itself, it is quite simple, right? So simplicity is an advantage of vertical scaling.

**Why Horizontal for High Traffic?**
In horizontal scaling, what happens is that we are increasing the server count. So when there is a very large application or when there is huge traffic, horizontal scaling is specified to handle them.

---

## ⚠️ Limitations of Vertical Scaling

So what should we choose? Because vertical scaling has some limitations.

1. **Hardware Limits:** It's impossible to add unlimited CPU & RAM to a single server. This is an obvious thing; how much power can you give to one resource? There is obviously a limitation. You cannot cross it.
2. **Single Point of Failure (No Backup/Failover):** You have only one server. You have increased the power, but you only have just one server. If there is a problem, if there is a failover, what will you do? If that one server dies, if that one server goes down, then the whole service will go down completely. You are totally dependent on only one server. There is no backup in vertical scaling, which is very risky for a business.

> **The Verdict:** That's why, for large scale applications, **Horizontal Scaling** is preferred due to the limitations of **Vertical Scaling**.

Obviously, if it's a very big application, it means it is being used by millions of users, and you don't want to take the risk of your service going down and impacting so many users. So people prefer horizontal scaling for large scale applications.

---

## ⏭️ Next Up

Later on, we will read our next topic: **Load Balancer**, which is quite important from an interview perspective. You might have heard this term in many places.
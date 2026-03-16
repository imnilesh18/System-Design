# 📊 Logging, Metrics, and Automation

## 🤔 Are they necessary?

So now we will read about Logging, Metrics, and Automation. What is their importance, what are they, and how do they help us?

* **Small Website:** If your server/website is small, meaning it is deployed on very few servers, then logging, metrics, and automation are **not a necessity**. It is fine not to have them; there is not much financial need.
* **Busy & Popular Website:** But let's assume your website becomes very busy and popular, and a very big business has been built from your website. Then doing logging, metrics, and automation is **essential**.

Let's look at what Logging, Metrics, and Automation are one by one.

---

## 📝 Logging

As the name 'logging' clearly suggests, if there is an error anywhere in your service, you should be able to monitor it, right? You should log any error somewhere so you can find out where the problem occurred and debug it.

```text
• LOGGING :- - Monitoring error logs
             - Per server level
             - centralized logging.
                 L> Example : New Relic

```

* **Per server level:** You can apply logging at the server level to find out which server the error came from.
* **Centralized Logging:** There are many tools where you can collect logs from all your servers in one place.
* *Example:* For example, New Relic, where the logs of all hosts and servers are gathered and displayed in one place. If an issue arises anywhere, we are able to point out which host or server it came from.



Logging is very helpful in debugging and finding where the bugs are.

---

## 📈 Metrics

Collecting different types of metrics is quite beneficial. It gives you business insights and lets you know about the health of your service.

```text
• METRICS :- - Gain business insights
             - Aware of health of our service
             - Host level metrics: CPU, Memory
                                   etc.
                       Example :- Grafana

             - Aggregated Level metrics :-
                       Performance of DB, cache
                       etc.

```

What kind of metrics am I talking about?

1. **Host level metrics:** This includes the CPU usage and memory of the host/server where your service is running. We have to keep track of these things. If the CPU usage goes up, alerts come, because it can cause problems. It's good to have a dashboard where you can check CPU and memory usage.
* *Example:* We use Grafana for this. There, we are able to see these metrics: CPU usage and memory usage of the service.


2. **Aggregated Level metrics:** This refers to the total performance of your DB, the total performance of your Cache, etc. You can see all these metrics in one place.
3. **Business metrics:** This is very important. You should know how many Daily Active Users there are and how much revenue your service is earning. We should know all these metrics, and we should be able to monitor these metrics for our service.

---

## 🤖 Automation

Automation is very important and is heavily used today everywhere.

When our system gets big and complex, we should use automation tools. This improves our productivity and saves us time. If tools can do this for us, manual effort will obviously decrease, right?

```text
• AUTOMATION :- - When system gets big & complex
                - Automation tools
                - Improve productivity

                - Continuous integration
                         Code check-in verified
                         through automation

                - Automating builds, test,
                  deployment etc.
                         Ex :- Jenkins Jobs
                  
                - Don't forget "Message Queue"

```

* **Continuous integration:** This is very helpful. For example, when we check in or commit code changes, it is automatically verified through automation to ensure there are no issues with our changes.
* **Automating builds, test, deployment etc.:** These can also be done automatically. Suppose you have checked in your changes and you need to build the service. You can create Jenkins jobs. In Jenkins, you can easily automate the build, which will also handle testing and deployment.

Automating is very, very important for a big and complex service. It reduces manual effort and increases our productivity.

**Don't forget the Message Queue!**
Remember, a Message Queue is also a kind of automated tool. The producer sends its jobs to the Message Queue, and the consumer picks them up and executes them later. My work is done automatically with the help of the Message Queue.

---

## 🗺️ The Complete Modern Architecture

Let's look at how much our service has improved overall!

```text
                          +-------+
                          | User  |
         +----------------+       +-----------------+
         |                +-------+                 |
         v                                          v
      +-----+                                    +-----+
      | DNS |                                    | CDN |
      +-----+                                    +-----+
         |                                          
         |
         |                 +---------------+
         +---------------> | Load balancer |
                           +---------------+
                                  | geoDNS
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
       | DC1                              +-------------+        |
       |                                  | [✉][✉][✉] |        |
       |             +-------------+      +-------------+        |
       |             |             |----->  Message Queue        |
       |             | Web servers |                             |
       |             |             |                             |
       |             +-------------+                             |
       |               /    |    \        +-------------+        |
       |              /     |     \       |             |        |
       |             v      |      \----->|   Workers   |        |
       |  +----------+      v             |             |        |
       |  |          |   +-------+        +-------------+        |
       |  |Databases |   | Caches|                               |
       |  |          |   +-------+                               |
       |  +----------+                                +-------+  |
       |                                              | NoSQL |  |
       |                                              +-------+  |
        - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                                  ^
                                  |
                   +-----------------------------+
                   | Logging        Metrics      |
                   |                             |
                   | Monitoring     Automation   |
                   +-----------------------------+
                                Tools

```

Let's start from the beginning:

1. The user got the Load Balancer's IP address from the **DNS**.
2. Whatever static content was needed was retrieved from the **CDN**.
3. The user connected to the **Load Balancer**.
4. With the help of **geoDNS**, the user is routed to the closest Data Center. (I have only shown Data Center 1 here to adjust the image, but there could be more).
5. Inside the data center, we have **Web Servers**.
6. For the **Database**, remember we used a Master-Slave DB model (though only a generic DB icon is shown here due to space). We also put a **Cache** above the database to reduce the load.
7. We set up a separate **NoSQL DB** (which we used earlier to store the user's state/session information).
8. Pay attention, I have also added the **Message Queue** now! The Web Servers (Producers) send messages to the Queue, and the **Workers** (Consumers) execute them.
9. And at the very bottom, what we discussed in this video: we are now doing **Logging, Metrics, Monitoring, and Automation**.

Because of this, we are able to see how our overall service is working. If an issue arises, we are able to see it. We can see how our overall service is performing, how much revenue it is generating, and how many active users there are because we are monitoring it. Most of these things are automated because we have implemented Automation.

Overall, our service has become very good! It's a piece of good news that our service has become big and popular, and we have made many improvements to it.
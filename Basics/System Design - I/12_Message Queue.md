# 🚀 Message Queue

## 🏗️ Modern Architectures & The Need for Decoupling

So now we are going to study a very important topic whose name is Message Queue. It is asked quite often in interviews what a Message Queue is, what its advantages are, and why we use it.

First, let's see what happened today that created the requirement for a Message Queue. Today, in modern architectures, what happens is that you decouple your application into very small, independent chunks or separate independent blocks.

```text
               Modern architectures :-
        
                   +---------------+
                   |  APPLICATION  |
                   +---------------+
                    /      |      \
                   /       |       \
               +----+   +----+   +----+   Smaller independent
               |    |   |    |   |    |   Building Blocks
               +----+   +----+   +----+
        
               - Easier to :-
                    -> Develop
                    -> Deploy
                    -> Maintain

```

Now, because these are small chunks, you can maintain them separately, develop them separately, and deploy them separately. Instead of focusing on one large chunk, you break it down into smaller, independent blocks that you can take care of separately. This makes things very easy. I can pick up one block and work on it separately, and take another block and work on it separately. We have decoupled them into smaller blocks, making them very easy to develop, easy to deploy, and easy to maintain.

> **The Big Question:** But pay attention to one big thing: how will communication happen between these different decoupled components? For example, if one chunk wants to communicate with another, how does communication happen between them?

This communication happens through a Message Queue. Message queues provide communication and co-ordination for them. Message queues are quite helpful.

---

## ✉️ The Message Queue Architecture

Let's move forward and see what the architecture of a Message Queue looks like. The Message Queue model looks quite simple.

In this simple model:

* On one side, there is a **Producer** that sends commands or orders.
* On the other side, there are **Consumers** (workers) that follow all those orders.

The Producer (also called a Publisher) publishes the commands. It keeps sending commands, and they keep getting stored there. The Consumer (also called a Subscriber), who has subscribed to the Message Queue, picks up those commands from the queue and works on them.

So one side is the Producer producing, and the other side is the Consumer consuming and performing whatever task or command message came in.

```text
"Message Queues provide communication and co-ordination for them."

Message Queue :-
• Supports Asynchronous communication.

               Publish                Consume
+-----------+ --------> +-----------+ --------> +-----------+
| Producer  |           | [✉] [✉] [✉] |           | Consumer  |
+-----------+ <-------- +-----------+ <-------- +-----------+
(Publishers)               Queue      SUBSCRIBE (Subscribers)

☆ Producer can produce a message to queue
  even when consumer is unavailable.
  
☆ Consumer can read message from queue
  even when producer is unavailable.

```

### ⏳ Asynchronous Communication

What does this mean? Let's assume the Consumer is not available right now. The Producer will just publish its messages and leave; the Producer becomes free. The messages are still in the queue. When the Consumer becomes available, it will consume the messages.

The Producer did its job by publishing and became free. The Producer is not dependent on the Consumer; the Producer and Consumer are totally independent. You can scale both separately, work on both separately, and make changes to them separately.

This is called **Asynchronous Communication**. There is no synchronization between the Producer and Consumer. The Producer can freely send a message anytime, and the Consumer can consume it anytime.

---

## 🖼️ Real-World Example: Photo Customization

Let's take a very good, small example to understand this clearly.

Let's assume you have a photo customization service that crops your photo, increases sharpness, blurs, or cleans your photo. And the customization processing takes quite a bit of time. Let's look at it through a diagram.

```text
 +-------------+      publish      +---------------------------+     consume     +---------------------------+
 |   _   _   _ |                   |                           |                 |   _   _   _               |
 |  |_| |_| |_|| ----------------> |       [✉] [✉] [✉]         | --------------> |  |_| |_| |_|              |
 |  | | | | | ||                   |                           |                 |  | | | | | |              |
 |  Producer   |                   | queue for photo processing|                 |  Consumer                 |
 +-------------+                   +---------------------------+                 +---------------------------+
   Web servers                                                                     Photo processing workers

```

Here is the Producer (the web server). It will send a command and publish to the message queue for photo processing, sending a message that photo1, photo2, and photo3 need to be customized.

What will the Consumer do? It will take all the photos one by one and process them.

The Producer is tension-free. The Producer had 100 requests, put all 100 in the queue, and now the Producer is free. Now the Consumer will pick up all the messages one by one, pick up the photos, and process/customize them.

Pay attention that the Producer was always free; the Producer did its job.

---

## ⚖️ Independent Scaling

As I mentioned, both are independent. Both Producer and Consumer are independent.

```text
+-----------+                       +-----------+
| Producer  |                       | Consumer  |
+-----------+                       +-----------+

-> Independent

-> Scaled Independently

-> Queue size large  [Up Arrow]
   add more consumers / workers
   
-> If Queue is empty mostly,  [Smiley Face]
   reduce consumers / workers

```

* **Scaling Up:** If, for example, a lot of requests come in and there are many photos in the queue, what will I do? I will increase the consumers. I will increase the number of workers. If there were 5, I will make it 10. I scaled the consumer, making it bigger by adding workers, so my processing will increase and work will be done easily. I only scaled the consumer; I did nothing to the producer.
* **Scaling Down:** Let's assume the requests are coming in very low, the queue size is very small (maybe just 5 requests). Then I don't need that many consumers. What will I do? I will reduce the consumers. From 10 consumers, I will just make it 2. I downscaled the consumers.

You can see I am independently scaling the consumer up or down, and it is not dependent on the producer. Both can be scaled separately.

---

## 🎯 Conclusion & Benefits

So the Message Queue is a very simple model; it just looks like this, there is a producer and a consumer. But it has many advantages!

You can see that you broke down your large service into smaller decoupled components, and the Message Queue is helping in the communication between them.

Your service will also be quite reliable because you broke it into different components. It's not like if one component breaks, the whole system will go down. You have different components and you can manage them separately. For example, if you broke your service into 5 components, and one has a problem, you know exactly where the issue is. You can independently work on it, and debugging becomes very easy.

Your system has become quite reliable, very easy, and very good. So this is all about Message Queues.
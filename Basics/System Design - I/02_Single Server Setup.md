# 🖥️ Single Server Setup

## 🌱 From Scratch: Single User to Millions

First, I will show a setup where there is a single server and a single user. Then, we will build a system that supports millions of users. We will have a single user, and we will move to millions of users.

`Single User` ➡️ *move to* ➡️ `Millions of Users`

Let's see how to do this single-user setup and how to build a single-user system. If someone asks you in an interview to build a sample single-user system, before that, I will tell you two things: what a Domain Name is and what an IP Address is.

---

## 🌐 Domain Names and IP Addresses

A domain name is nothing but what we type in the search bar. Because it is in English, it is easy for us to remember. But the server does not understand this. The server understands the IP Address.

**Examples:**

* **Domain Name:** `www.leetcode.com` (This is for us to understand)
* **IP Address:** `15.125.23.214` (What is this??)

We need to convert the Domain Name we type into an IP Address to be able to talk to the server. How do we get the IP Address from a Domain Name? That is the job of the DNS (Domain Name System).

> **DNS Definition:** It is generally a third-party application or service provider that gives us the service of returning the IP Address from a given Domain Name.

---

## 🏗️ The Single Server Architecture

If you understand the above, this small diagram will make it very easy to understand how a single server and single user setup works. There is a single user who has a laptop with a web browser open, or a mobile app. For example, they want to open a website like `api.mysite.com` or `www.leetcode.com`.

Here are the steps:

```text
+-----------------------------------------+
|                  User                   |           (1) api.mysite.com (Domain name)
|                                         | --------------------------------------------> +-------+
|   [Laptop]            [Mobile]          |                                               |       |
|  Web browser         Mobile app         | <-------------------------------------------- |  DNS  |
|                                         |           (2) 15.125.23.214 (IP address)      |       |
+-----------------------------------------+                                               +-------+
        |                           ^
        |                           |
        | 15.125.23.214             | (4) HTML page
        |                           |
        v                           |
+-----------------------------------------+           
|                                         |           Example:-
|               [Server]                  |           www.leetcode.com
|              Web server                 |           
|                                         |
+-----------------------------------------+

```

1. **Step 1:** The request goes to the DNS asking for the domain name (e.g., `api.mysite.com`).
2. **Step 2:** The DNS gives us the corresponding IP address (e.g., `15.125.23.214`).
3. **Step 3:** A request is then sent directly to the Web server using that specific IP address (`15.125.23.214`).
4. **Step 4:** The Web server gives us an HTML page as a response, which we can then see on our laptop or phone screen.

It is quite a simple step and easy to understand: there is one user and one server, and you just ask the server for what you need.

---

## 🚦 Understanding Traffic

Now let's see where traffic comes to our server from. First, what does the word "traffic" actually mean? I don't want to use any terms that create confusion.

> **Traffic Analogy:** Think of a restaurant that has only one chair and one customer. If the restaurant becomes famous and multiple customers come, the traffic increases. Similarly, in systems, traffic means the number of requests coming in, or the number of people requesting things.

When traffic increases, problems will obviously occur.

**TRAFFIC Sources:**

* Web applications
* Mobile Apps

---

## 📱 Sources of Traffic: Web vs. Mobile

Traffic comes to the server from two main places: Web Applications (when you search via a web browser) and Mobile Apps.

* **Web Applications:** These utilize a server-side language (like Java, Python, etc.) combined with a client-side language that helps with the UI (like HTML, JS).
* **Mobile Apps:** These communicate with the server mainly using the HTTP protocol. The API response that comes back from their communication is in JSON format.

Later in the series, we will get more clarity regarding JSON and other response formats. Here is a small example of what a JSON API request and response looks like, using key-value pairs:

```java
GET /users/12
{
    "Name": "Iron Man",
    "Age": "50",
    "Movie": "Infinity War"
}

```

---

## 📈 The Need to Scale

Here our single server setup ends. We have understood this setup for a single user.

But with the growth of users, our "1" server is not enough. ☹️
It is a big problem; how much can one poor server handle when millions of users arrive?

We need more servers. ✔️

So we will move towards the next step: how to scale. We need to learn how to design the service so it can handle requests from a much larger number of people.
# 🌍 Data Centers & geoDNS

## 📈 The Need for Multiple Data Centers

So now we will study a very interesting topic: Data Centers. Let's assume that your website has become very popular around the world. A lot of users are using it internationally as well.

In such a case, you need to improve your availability and provide a good user experience because a lot of users are using it internationally. To give a good user experience and improve availability across wider geographical areas, what will we do? We will set up our data centers in different geographical areas.

```text
To improve availability & better user experience 
across wider "geographical" areas, we should support

            "Multiple Data Centers"

```

Let's assume you live in the United States. We set up one data center in the Eastern part of the United States, and we set up a separate data center in the Western part of the United States.

* United States (East) has **Data Center 1**
* United States (West) has **Data Center 2**

We have set this up. Now the requests that come in, some percentage of requests will go to Data Center 1 in the US East, and some traffic/requests will go to the US West (Data Center 2).

---

## 🗺️ Traffic Routing with geoDNS

How is this routing happening (some requests going to Data Center 1, some to Data Center 2)? This is possible because of **geoDNS**.

geoDNS does nothing but take a Domain Name as input, and it returns an IP address, but *based on the location of the user*.

This means if you are sitting in the United States East and you make a request from there, the geoDNS will send you the IP address according to your user location. It will send the IP address for Data Center 1, so your request will go to Data Center 1. Similarly, if you are sitting in the US West, your request will go to Data Center 2 because geoDNS helps in routing and will send your request to Data Center 2.

```text
geoDNS :-
  • Route to closest data center.

  • Example :- US (east) -> [ Data Center - 1 ]
               US (west) -> [ Data Center - 2 ]

  • Split traffic :-  x % -> US (East)
                   (100-x)% -> US (west)

       Domain Name
           |
           v
        +-----+
        | DNS | -----> IP address based on (location of user)
        +-----+

```

---

## 🏗️ Multi-Data Center Architecture

Let's look at this through a diagram.

We have placed the entire setup of Data Center 1 in US East, and a setup in US West. Data Center 1 is on the left side, and Data Center 2 is on the right side. The rest of the things are the same; we have just built our setup in different locations.

```text
                                  +-------+
                                  | User  |
                                  +-------+
                                      |
                                      v
                             +-----------------+
                             |  Load balancer  |
                             +-----------------+
                           Geo-routed /   \ Geo-routed
                                     /     \
               Data Centre - 1      v       v       Data Centre - 2
           - - - - - - - - - - - - - -     - - - - - - - - - - - - - - 
          |       DC1 US-East         |   |       DC2 US-West         |
          |       +---------+         |   |       +---------+         |
          |       |Web srvr |         |   |       |Web srvr |         |
          |       +---------+         |   |       +---------+         |
          |        /       \          |   |        /       \          |
          |       v         v         |   |       v         v         |
          |  +--------+ +---------+   |   |  +--------+ +---------+   |
          |  |  DBs   | | Caches  |   |   |  |  DBs   | | Caches  |   |
          |  +--------+ +---------+   |   |  +--------+ +---------+   |
           - - - - \ - - - - - - - - -     - - - - - - - / - - - - - - 
                    \                                   / 
                     \-----------> +---------+ <-------/
                      Session data | {} NoSQL| Session data
                                   +---------+

```

---

## 🚨 Handling Outages

Now let's assume our Data Center 2 in US West goes down. Let's assume an outage happens there.

If an outage occurs, all the requests that were supposed to go to Data Center 2 will have to be routed back to Data Center 1. This means we need 100% of the traffic to go to Data Center 1.

```text
                             +-----------------+
                             |  Load balancer  |
                             +-----------------+
                100% traffic  /             X   Outage
                             /               \
                            v                 v
               Data Centre - 1               Data Centre - 2
           - - - - - - - - - - - - - -     - - - - - - - - - - - - - - 
          |       DC1 US-East         |   |          X   X            |
          |       +---------+         |   |            |              |
          |       |Web srvr |         |   |            O              |
          |       +---------+         |   |                           |
          |                           |   |                           |
           - - - - - - - - - - - - - -     - - - - - - - - - - - - - - 

```

This is quite a big challenge. How are these technical challenges addressed? This routing is actually done with the help of geoDNS. geoDNS helps us in this routing so that if there is an outage in Data Center 2, it routes all the requests to Data Center 1.

---

## 🛠️ Technical Challenges for Multi-Data Center Setup

As I mentioned, what are the technical challenges for setting up multiple data centers?

```text
" Technical Challenges to achieve multi-data center setup :- "

  • Traffic redirection :- geoDNS  :)

  • Data Synchronization :- Replicate data across 
                            multiple data centers.

  • Test and Deployment :- Test your website/app
                           at different locations.

```

1. **Traffic redirection:** Addressed by geoDNS. If an outage happens in one data center, our requests will have to be routed to the other data center, which geoDNS helps with.
2. **Data Synchronization:** It's an obvious thing that users use the local databases and local caches of their own regions. Let's assume I am sitting in US East and Data Center 1 has an outage (goes down). My requests will be routed to Data Center 2. But what if the data I need for the response was in Data Center 1 and not in Data Center 2? The routing happened, my request went to Data Center 2, but the response I need isn't there, which will cause a huge problem. Therefore, we replicate our data across multiple data centers so that no matter which data center my request is routed to, I get the response I need. This is called Data Synchronization.
3. **Test and Deployment:** This is very important. Obviously, if your data center is set up at different locations, you should test your website or service in every location. This ensures that your service/website is working in all locations.
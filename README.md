# 📘 System Design — Easy Study Notes

> Beginner-friendly System Design notes, written so **anyone** can understand — even with zero background.
> Based on the [System Design One Shot Full Course by Telusko](https://www.youtube.com/watch?v=Vnm-ycSfJx4).
>
> **Why learn this?** *"Anyone can write code that works. System Design is what makes it work for a million people at once."*

---

## 📑 Table of Contents

| # | Topic | Status |
|---|-------|--------|
| 1 | [What is System Design?](#1-what-is-system-design) | ✅ |
| 2 | [Case Study: Designing "Alien Bank"](#2-case-study-designing-alien-bank) | ✅ |
| 3 | [Core Components of System Design](#3-core-components-of-system-design) |  ✅ |
| 4 | [Types of Applications: Data vs Compute Intensive](#4-types-of-applications-data-intensive-vs-compute-intensive) | ✅ |
| 5 | [Functional & Non-Functional Requirements](#5-functional--non-functional-requirements) | ✅ |
| 6 | DNS (Domain Name System) | 🔜 Coming soon |
| 7 | APIs (REST, GraphQL, gRPC, WebSockets) | 🔜 Coming soon |
| 8 | SQL & NoSQL Databases | 🔜 Coming soon |
| 9 | Caching | 🔜 Coming soon |
| 10 | Load Balancers | 🔜 Coming soon |
| 11 | Replication & Partitioning | 🔜 Coming soon |
| 12 | CAP Theorem | 🔜 Coming soon |
| 13 | Message Queues & Pub-Sub | 🔜 Coming soon |
| 14 | Fault Tolerance, Monitoring & Observability | 🔜 Coming soon |
| 15 | Case Study: Streaming Application | 🔜 Coming soon |

---

## 1. What is System Design?

### Why should you care?

- When you learn a language or framework, you can build an app that works for **10 users**.
- But will it work when **10 million users** hit it *at the same time*? That's the real question.
- WhatsApp, YouTube, Instagram, Amazon — millions of people use them simultaneously, and they still work. **System Design is the reason.**
- In interviews, companies rarely say "this is a system design round," but they expect you to think in these terms and solve complex problems.

### Breaking down the words: "System" + "Design"

Systems exist all around us. Your body has a *digestive system* — many organs working together for one purpose. But you didn't build it, so you can't change it much.

The systems in the IT industry are **built by engineers like us** — which means we *can* choose the components, improve them, and redesign them based on the traffic and load we get.

**Definition:**

```
System = Components + Common Goal
```

- **Components** → the building blocks (servers, databases, caches…)
- **Common Goal** → all of them work together to solve one purpose (e.g., "let people watch videos")

**Design** is deciding *which* components to pick and *how* to connect them, based on requirements.

> 💡 **Simple example:** A cricket team is a "system." Batsmen, bowlers, and a wicketkeeper (components) all play different roles, but share one common goal — winning the match.

---

## 2. Case Study: Designing "Alien Bank"

The best way to understand system design is a story with **zero technology** in it. Imagine we open a small bank — **Alien Bank** — with just:

- **1 cashier** sitting at **1 cash counter**
- Customers who come to **deposit or withdraw** money

**Customer flow:** Visit counter → Deposit/Withdraw → Take receipt → Done.

Now let's grow the bank and face **5 real issues** — every issue maps to a core system design concept.

### Issue 1: The process is slow ⏳

The cashier takes **10 minutes per customer** → only 6 customers served per hour. In rush hour, the system breaks.

**Fix:** Don't buy anything new — make the cashier *better*. Train them to count cash faster and type receipts faster. Now: **5 min/customer (50% improvement!)**

> 🧠 **System design translation:** Improve your **code quality** first. Optimize slow loops, use better algorithms (this is where DSA and Low-Level Design help). No new hardware needed.

### Issue 2: Customer count is increasing 📈

The cashier is already at peak skill — we can't train them more.

**Fix:** Upgrade the *counter itself* — a bigger desk, a **cash-counting machine**, and pre-filled forms so the cashier doesn't waste time asking questions. Now: **3 min/customer.**

> 🧠 **System design translation:** This is **Vertical Scaling** — making your *single server* more powerful (more RAM, better CPU). ⚠️ Note: there's always a *limit* to how much you can upgrade one machine.

### Issue 3: Customers still wait too long 🧍🧍🧍

Cashier maxed out. Counter maxed out. If 10 customers are in line, the 10th waits **27 minutes** for a 3-minute task.

**Fix:** Open a **second cash counter**.

> 🧠 **System design translation:** This is **Horizontal Scaling** — adding *more servers* instead of upgrading one.

### Issue 4: The two counters don't share data 🗂️

Each counter keeps its own records. Danger:

- Counter 2 doesn't know a customer who registered at Counter 1.
- Worse — a customer withdraws money at Counter 1, then withdraws *again* at Counter 2, because the counters never told each other! (**data discrepancy**)

**Fix:** Both counters read/write from **one central record book**.

> 🧠 **System design translation:** This is a **Centralized Database** — one source of truth that all servers validate against.

### Issue 5: One counter is overloaded, the other is idle 😴

The bank's entrance is near Counter 1, so everyone lines up there while Counter 2 sits idle.

**Fix:** Put a **middleman** at the door. He checks how busy each counter is and directs each new customer to the less-crowded one. If Counter 1 breaks down, he sends *everyone* to Counter 2 until it's fixed.

> 🧠 **System design translation:** This is a **Load Balancer**. It does two jobs:
> 1. **Health checks** — is each server alive and working?
> 2. **Distributes requests** evenly across servers.

### 🗺️ The full mapping (bank → real system)

| Bank world | System design world |
|---|---|
| Customers | Requests |
| Queue of customers | Traffic |
| Cashier (the person working) | Application code |
| Cash counter | Server |
| Training the cashier | Code optimization (DSA/LLD) |
| Bigger desk + cash machine | Vertical scaling |
| Adding a second counter | Horizontal scaling |
| Central record book | Centralized database |
| Middleman at the door | Load balancer |

---

## 3. Core Components of System Design

Every app you use — WhatsApp, Instagram, YouTube, Netflix, Amazon — is really just showing you **data** in 4 forms: **images, videos, audio, or text**.

> **At the core of every system is data**, and the whole design is about making that data reach users the way they expect — fast and safely.

Here are the building blocks, and *why* each layer exists:

### 3.1 Database 🗄️

- **Job:** Store data, and return it whenever asked. (Drawn as a cylinder in diagrams.)
- Two big families: **SQL** (tables) and **NoSQL** (documents, key-value, graphs…) — covered in detail later.

### 3.2 Application Code (Backend Server) ⚙️

**Why does this layer exist?** Imagine if every user of LinkedIn had to write database queries themselves to see a profile. Impossible! Normal users don't know SQL.

So we put **application code** in front of the database. It:

- **Hides the complexity** of tables/documents.
- Exposes simple **endpoints (APIs)** like `GET https://telusko.com/students` → returns all students.
- Handles operations: **insert, update, retrieve, delete** data.
- Talks to the database over connections (IP / HTTP / TCP).

Bonus: once your app exposes APIs, *other applications* (not just your own app) can also use them.

### 3.3 Client Application 📱

**Why?** Even calling APIs and reading JSON is too technical for normal users. So we add a friendly face on top:

- Mobile app, website, or even an **ATM machine** — anything the user directly touches.
- The client talks to the application code, which talks to the database.

**The basic flow of almost every app:**

```
Client (mobile/web)  →  Application Code (server)  →  Database
        ↑ ________________ data comes back ________________|
```

This is what your college project (a to-do app or simple chat app) looks like!

### 3.4 Cache ⚡

**Problem:** Database calls are heavy. A query joining 12 tables takes time — doing it for *every* request is wasteful.

**Fix:** Keep **frequently used data** in a fast, temporary store called a **cache**, so we skip the database entirely for popular data.

> 💡 **Simple example:** You keep your daily-use items (keys, wallet) on a table near the door instead of inside a locked cupboard. The cupboard (database) is safe but slow; the table (cache) is fast.

### 3.5 Load Balancer ⚖️

When you have multiple servers, who decides which server handles your request? The **load balancer** sits in the middle and:

1. Checks **server health** (is it alive?).
2. **Divides requests equally** among servers.

(Exactly the "middleman" from Alien Bank.)

### 3.6 Message Queue 📬

One operation often means **many tasks**. Placing one Amazon order = update inventory + process order + arrange delivery + send SMS/email confirmation.

Two ways services can talk:

- **Synchronous:** Service A calls Service B and **waits** for a reply before moving on.
- **Asynchronous:** Service A drops the request and **moves on** — no waiting.

For async work, we put a **message queue (broker)** in the middle:

```
Order Service  →  [ Message Queue ]  →  SMS Service
   (drops request & moves on)          (processes one by one)
```

The queue **stores** every incoming request, **delivers** them one by one, handles **retries** if something fails, and reports back statuses. Vital in microservice environments.

> 💡 **Simple example:** A restaurant order board. Waiters (order service) pin orders on the board and go back to serving. Chefs (SMS service) pick orders one by one. Waiters never stand and wait in the kitchen.

### 3.7 Monitoring & Logs 📊

With so many components (client → load balancer → servers → databases → queues), we must watch them all:

- Is the load balancer working? Does the application code have errors?
- **Logs** give us the full stack trace to reproduce and fix bugs.
- When we ship a new feature, monitoring tells us if it broke an old one.

### Full picture

```
                        ┌──────────────┐
   📱 Client  ──API──►  │ Load Balancer │
                        └──────┬───────┘
                       ┌───────┴───────┐
                       ▼               ▼
                  [ Server 1 ]    [ Server 2 ]      ⚡ Cache
                       │               │
                       └───────┬───────┘
                               ▼
                          🗄️ Database(s)
                               │
                        📬 Message Queue ──► other services
       (📊 Monitoring & Logs watch over everything)
```

---

## 4. Types of Applications: Data-Intensive vs Compute-Intensive

**Don't jump straight to drawing diagrams!** The *first* question before designing anything:

> **"What type of application is this — data-intensive or compute-intensive?"**

Getting this right saves **money, time, and complexity**, because components aren't free — putting effort (and money) into the wrong component is bad system design.

### 4.1 Data-Intensive Applications 🗃️

The core focus is **data**: storing it, moving it, updating it, serving it. Calculations are simple — the challenge is the sheer *volume and movement* of data.

**Examples:**

- **Instagram feed** — fetch posts, sort by time/relevance, show images/videos, like/comment/share. No complex math anywhere!
- **WhatsApp messages** — millions of messages a day, all just stored and delivered.
- Bank transactions, analytics dashboards, log processing systems.

**Typical worries:**

- How **fast** can we read the data?
- How **safely** do we store it?
- How many users can use the same data **simultaneously**?
- What if a **server / network / database machine dies** — how do we protect the data?

**Where we fix problems:** databases, caching, replication, sharding, data consistency — NOT the user's CPU. *(New terms? Don't worry — each gets its own section later.)*

**How Instagram scales (mini case study):**

- Multiple databases to store data.
- **Aggressive caching** for fast reads.
- Images/videos aren't stored in the database directly — they go to a **CDN** (a network of servers that serve files fast); the database only stores the file's *location*.
- Notice: fixing Instagram's latency **barely touches the CPU/GPU** — the issue is data movement between components.

### 4.2 Compute-Intensive Applications 🧮

The exact opposite: **little data in and out, but heavy calculations**. Focus shifts from databases/caches to **CPU and GPU power**.

**Examples:**

- Image processing / video rendering
- Machine learning model training
- Simulations (e.g., flight simulators)
- Cryptography

**Typical worries:**

- How **fast** can we compute?
- Can we run tasks **in parallel**? (e.g., an image processes in the background while you keep using the app)
- How do we **reduce computational cost**?
- Should we use a **GPU instead of a CPU**?

**Flight simulator (mini case study):** Training pilots on real aircraft is too expensive and risky, so we build simulators that must behave *exactly* like the real plane. Almost no data to fetch — but massive real-time computation. Worries: parallelism, GPU vs CPU, cost, and efficient algorithms.

### 4.3 The trick to tell them apart 🎯

> **Where is the time being lost?**
> - Lost in **moving data** around → *data-intensive* → fix databases, cache, network.
> - Lost in **calculations** → *compute-intensive* → fix CPU/GPU, algorithms, parallelism.

⚠️ **One app can be both!** Apps have many features, and the type depends on the *feature*:

- YouTube **serving videos** → data-intensive.
- YouTube **recommendations** (analyzing what you watch) → compute-intensive.

So diagnose per-feature, then fix the right component.

---

## 5. Functional & Non-Functional Requirements

Before building any system, we split requirements into two buckets.

### 5.1 Functional Requirements ✅ — *"WHAT the system does"*

Every **feature**, every **action a user can take**, all the *working* of the app.

**Example — Amazon (any e-commerce app):**

1. User can **register**
2. User can **log in**
3. User can **view products**
4. User can **search** products
5. User can **filter** products
6. User can **add products to cart**
7. User can **apply coupon codes** for discounts
8. User can **place an order**
9. User can **make a payment**
10. User can **track the order**

That's it — list the features, and you have the functional requirements.

### 5.2 Non-Functional Requirements 🚀 — *"HOW WELL the system does it"*

These don't add features — they define **quality**. And they must be **specific and measurable**, not vague.

**Example — Amazon again:**

| Vague ❌ | Specific ✅ |
|---|---|
| "Handle many users" | Handle **1 million daily active users** |
| "Should be fast" | Response time **< 200 ms** per request |
| "Should rarely go down" | **99.9% uptime** (defined via SLAs/SLOs) |
| "Should be secure" | Data must be **encrypted** |
| "Handle sales traffic" | Handle **10× normal traffic** on sale days (Black Friday / Diwali) |
| "Shouldn't crash" | System must be **fault tolerant** — detect and fix faults fast |

### 5.3 The generic list of Non-Functional Requirements

- **Scalability** — When traffic outgrows your server, do you scale *horizontally* (more servers) or *vertically* (bigger server)?
- **Availability** — How much uptime do you promise? Defined in:
  - **SLA (Service Level Agreement):** a *written contract* — e.g., AWS promises max ~5 min downtime/day. If they break it, customers can even sue.
  - **SLO (Service Level Objective):** the internal target backing that promise (e.g., 99.9% uptime).
- **Reliability** — The system behaves correctly: **no data loss**.
- **Performance** — How fast, measured by latency percentiles: **P90 / P95 / P99** (e.g., P99 = 99% of requests finish within X ms).
- **Security** — **Authentication** (who are you?), **Authorization** (what are you allowed to do?), **Encryption** (scramble data so outsiders can't read it).
- **Maintainability** — Code kept in clean, separate **modules** so pieces can be reused and changed easily.
- **Observability** — After going to production, users behave unpredictably. **Monitoring + logging** track behavior, catch bugs, and drive improvements.

### 5.4 One-line summary 🧾

> **Functional = WHAT the system does** (its features).
> **Non-Functional = HOW the system performs** (speed, scale, uptime, security).

> ✍️ **Exercise (from the course):** Pick any app you use daily — Instagram, YouTube, Netflix, WhatsApp, Flipkart. Write down 3–4 functional and 3–4 non-functional requirements for it. This one habit builds the system design mindset.

---

## 🔜 Next up: DNS (Domain Name System)

*How does typing `youtube.com` actually reach a server? Root servers, TLDs, name servers, and the full DNS resolution flow — notes coming after the next study session.*

---

### 🙏 Credits

These notes follow the **[System Design One Shot Full Course](https://www.youtube.com/watch?v=Vnm-ycSfJx4)** by **Telusko** (instructors: Navin Reddy & Akshay Agarwal). All credit for the teaching and examples goes to them — watch the video for the full experience. Notes are for personal study and community learning.

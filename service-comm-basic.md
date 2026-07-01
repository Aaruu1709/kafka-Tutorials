# kafka-Tutorials
Service-to-Service Communication
In microservices, services talk to each other in two ways:

In microservices, communication happens in two ways: synchronous and asynchronous. In synchronous communication, one service calls another and waits for response using REST Template, WebClient, or Feign Client. In asynchronous communication, services communicate using message brokers like Kafka or RabbitMQ, where the sender does not wait for response. Sync is used for immediate response, while async is used for decoupled and scalable systems like notifications and order processing.
# Synchronous Communication in Microservices (Interview Notes)

In microservices, synchronous communication means **one service calls another service and waits for the response** before continuing execution.

---

# 1️⃣ REST Template (Legacy Approach)

## What is it?

> REST Template is a synchronous (blocking) HTTP client used to call REST APIs in Spring.

## 5 Important Points

* It is a **blocking client** (thread waits for response).
* Used for **synchronous API communication**.
* Simple and easy to use in older Spring applications.
* Now mostly **deprecated in favor of WebClient**.
* Not suitable for high-performance reactive systems.

---

# 2️⃣ WebClient (Modern Approach)

## What is it?

> WebClient is a modern HTTP client from Spring used for both synchronous and asynchronous communication.

## 5 Important Points

* Supports **both sync and async calls**.
* Non-blocking and reactive (based on WebFlux).
* Better performance than RestTemplate.
* Recommended for **modern microservices architecture**.
* Can handle **high concurrency efficiently**.

---

# 3️⃣ Feign Client (Spring Cloud Approach)

## What is it?

> Feign Client is a declarative REST client used to simplify service-to-service communication in Spring Cloud.

## 5 Important Points

* Very **simple and declarative (interface-based)**.
* Internally uses REST calls (via HTTP client).
* Reduces boilerplate code.
* Easy integration with **Spring Cloud LoadBalancer & Eureka**.
* Most commonly used in **Spring Boot microservices**.

---

# 🔁 Sync Communication Summary

## What is Sync Communication?

> Synchronous communication means the calling service waits for the response from the target service.

---

## 30-Second Interview Answer

> In synchronous communication, one microservice calls another microservice and waits for the response. In Spring Boot, we use REST Template (legacy), WebClient (modern reactive client), and Feign Client (declarative REST client). REST Template is blocking and outdated, WebClient is non-blocking and recommended for modern systems, and Feign Client is widely used in microservices for clean and simple service-to-service communication.

---

## One-Line Summary

> Sync Communication = Service calls another service and waits for response (blocking call).


-----------------------------------------------------


rest template 

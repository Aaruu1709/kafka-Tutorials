# Apache Kafka - Module 1 (Introduction)

## What is Apache Kafka?

Apache Kafka is a distributed messaging system used for asynchronous communication between applications. It allows producers to send messages and consumers to process them independently.

Kafka is widely used in microservices for real-time data processing, high throughput, and reliable message delivery.

---

## Why Do We Use Kafka?

### 1. Asynchronous Communication

* Producer sends messages without waiting for consumers.
* Improves application performance and response time.

### 2. Loose Coupling

* Services communicate through Kafka instead of direct API calls.
* One service failure does not immediately impact others.

### 3. High Throughput

* Kafka can process millions of messages efficiently.
* Suitable for large-scale enterprise applications.

### 4. Reliable Message Storage

* Messages are stored for a configurable retention period.
* Consumers can process messages later if needed.

---

## Real Project Example

Employee Management System:

Employee Service creates an employee and publishes an event to Kafka.

Multiple services consume the same event:

* Email Service → Sends welcome emails
* Notification Service → Sends notifications
* Audit Service → Stores audit logs

This enables asynchronous communication and loose coupling between services.

---

## Important Kafka Components

### Producer

A producer sends messages to Kafka topics.

Example:
Employee Service

### Consumer

A consumer reads messages from Kafka topics.

Example:
Email Service

### Topic

A topic is a logical channel where messages are stored.

Example:
employee-created

### Broker

A Kafka server is called a broker.

Example:
localhost:9092

### Partition

A topic can be divided into multiple partitions for parallel processing and better performance.

---

## Memory Trick

Producer → Topic → Broker → Consumer

* Producer sends messages.
* Topics store messages.
* Brokers manage topics.
* Consumers read messages.

---

## Interview Answer

**What is Apache Kafka?**

Apache Kafka is a distributed messaging system used for asynchronous communication between applications. It allows producers to send messages and consumers to process them independently. It is widely used in microservices for real-time data processing, high throughput, and reliable message delivery.

---
### Module 2: Kafka Setup and First Message
ZooKeeper is a centralized service
ZooKeeper helps Kafka brokers coordinate with each other and keeps information about topics, partitions, and leaders
Kafka Before 3.x
Kafka + ZooKeeper

ZooKeeper was mandatory.

Kafka 3.x and Above
Kafka (KRaft Mode)

ZooKeeper is optional because Kafka can manage metadata itself using KRaft modeDo we still need ZooKeeper in Kafka?
In older Kafka versions, ZooKeeper was required for broker coordination and metadata management. However, newer versions support KRaft mode, where Kafka manages metadata internally, reducing the dependency on ZooKeeper.
---

### Module 2: First Kafka Practical (Industry Style)


What is a Cluster ID in Kafka?

A Cluster ID is a unique identifier for a Kafka cluster. In KRaft mode, it is generated once and used to initialize the metadata storage for the cluster
command 1:
PS C:\Users\DELL> cd D:\kafka
PS D:\kafka> .\bin\windows\kafka-storage.bat random-uuid
yw724B1NSj6JwmunfGUDGA 
this is generated clustur id

Step 1: Format Kafka Storage (One-Time Setup)

.\bin\windows\kafka-storage.bat format `
-t yw724B1NSj6JwmunfGUDGA `
-c .\config\kraft\server.properties

Note: In PowerShell, use the backtick (`) for the next line.
Or run it in a single line:

.\bin\windows\kafka-storage.bat format -t yw724B1NSj6JwmunfGUDGA -c .\config\kraft\server.properties

You should see something like:

Formatting metadata directory ...
In KRaft mode, we generate a unique cluster ID and format the metadata directory before starting Kafka. This initializes the storage used by the Kafka cluster.

Step 2: Start Kafka Server
After formatting succeeds, run:
.\bin\windows\kafka-server-start.bat .\config\kraft\server.properties
Keep this terminal open.

If Kafka starts successfully, you'll see lots of logs and no errors.

A Cluster ID is a unique identifier for a Kafka cluster. In KRaft mode, it is generated once and used to initialize Kafka metadata storage.

What is KRaft Mode?

KRaft (Kafka Raft Metadata Mode) allows Kafka to manage metadata internally without ZooKeeper. It simplifies deployment and is the recommended approach for modern Kafka applications.

# Apache Kafka - Module 2 (KRaft Setup)

## Generate Cluster ID

```bash
.\bin\windows\kafka-storage.bat random-uuid
```

Example:

```text
yw724B1NSj6JwmunfGUDGA
```

---

## Format Kafka Storage

```bash
.\bin\windows\kafka-storage.bat format -t yw724B1NSj6JwmunfGUDGA -c .\config\kraft\server.properties
```

This command initializes the metadata storage required by Kafka in KRaft mode.

---

## Start Kafka Server

```bash
.\bin\windows\kafka-server-start.bat .\config\kraft\server.properties
```

Default broker address:

```text
localhost:9092
```

---

## Interview Notes

### What is a Cluster ID?

A Cluster ID is a unique identifier for a Kafka cluster. It is generated once and used to initialize Kafka metadata storage in KRaft mode.

### What is KRaft Mode?

KRaft (Kafka Raft Metadata Mode) is Kafka's built-in metadata management system that removes the dependency on ZooKeeper. It simplifies the architecture and improves maintainability.
---------------------------------------

What is a Kafka Broker?

A Kafka broker is a server that receives, stores, and delivers messages between producers and consumers.

What is localhost:9092?

localhost:9092 is the default address where producers and consumers connect to the Kafka broker.

What happens when Kafka Server starts?

When Kafka starts, it initializes metadata, loads topics and partitions, starts network listeners, and waits for producers and consumers to connect.


--------------------------------------
```
Module 3: Create Our First Topic
Keep the Kafka server terminal OPEN.

Open a NEW PowerShell window and run:

cd D:\kafka

Step 1: Create a Topic

Run:

.\bin\windows\kafka-topics.bat `
--create `
--topic employee-created `
--bootstrap-server localhost:9092 `
--partitions 3 `
--replication-factor 1

Or as a single line:

.\bin\windows\kafka-topics.bat --create --topic employee-created --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
What is happening?
employee-created
│
├── Partition 0
├── Partition 1
└── Partition 2

We created:

Topic Name: employee-created
Partitions: 3
Replication Factor: 1 (because we have only one broker)


What is a Topic?

A topic is a logical channel in Kafka where messages are stored and consumed by applications.

What is a Partition?

A partition is a subdivision of a topic that allows messages to be processed in parallel and improves scalability.

Multiple partitions help distribute data across consumers and increase throughput by enabling parallel processing.

Different consumers can process different partitions simultaneously.
Run:
```
.\bin\windows\kafka-topics.bat --create --topic employee-created --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
```
Then send me the output.

After that, we'll:

🔥 List topics
🔥 Produce our first message
🔥 Consume that message
If a topic already exists, Kafka will throw:

TopicExistsException
Interview Answer

If we try to create a topic that already exists, Kafka throws a TopicExistsException. We can either use the existing topic or create a new one with a different name.



---
Module 4: List and Describe Topics
Step 1: List All Topics

Run:

.\bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092

Expected output:

employee-created


--------------------------------------
Step 2: Describe the Topic

Run:

.\bin\windows\kafka-topics.bat --describe --topic employee-created --bootstrap-server localhost:9092

You will see something like:

Topic: employee-created
PartitionCount: 3
ReplicationFactor: 1

Partition: 0
Leader: 1

Partition: 1
Leader: 1

Partition: 2
Leader: 1


-----------------------------------------------------------
```
Module 5: Producer and Consumer (Hands-On)
```
---
Step 1: Start a Producer

Open a new PowerShell window (keep the Kafka server running).

Go to Kafka folder:

cd D:\kafka

Run:

.\bin\windows\kafka-console-producer.bat --topic employee-created --bootstrap-server localhost:9092

Now you'll see a blinking cursor.

Type:

Employee 101 Created
Employee 102 Created
Employee 103 Created

Press Enter after each message.

These messages are now stored inside Kafka.
Kafka automatically decides which partition will store each message.

Step 2: Start a Consumer

Open another PowerShell window.

Run:

cd D:\kafka

Then:

.\bin\windows\kafka-console-consumer.bat --topic employee-created --from-beginning --bootstrap-server localhost:9092

Expected output:

Employee 101 Created
Employee 102 Created
Employee 103 Created

---


A producer is an application that sends messages to a Kafka topic.
A consumer is an application that reads and processes messages from a Kafka topic.
What does --from-beginning mean?

The --from-beginning option tells the consumer to read all available messages from the start of the topic instead of only new messages.
---


----------------------------------------------------------

```
Module 6: Offsets

An offset is a unique number assigned to every message within a partition.

Kafka uses offsets to track which messages have already been consumed.

Why Do We Need Offsets?

Without offsets, Kafka wouldn't know:

Which messages were already processed
Which messages are still pending
From where a consumer should restart after a failure

1. What is a Consumer Group?
Interview Definition

A Consumer Group is a collection of consumers that work together to consume messages from a topic. Each message is processed by only one consumer within the group.
```



```Why do we create multiple partitions?

Answer:

We create multiple partitions to achieve parallel processing, improve throughput, and allow multiple consumers in the same consumer group to process messages simultaneously.

Another Important Question
What happens if one consumer crashes?

Answer:

Kafka performs rebalancing. The partitions assigned to the failed consumer are reassigned to the remaining consumers in the group.

```


```
What is Consumer Rebalancing?

Consumer rebalancing is the process of redistributing partitions among active consumers when a consumer joins or leaves the group.

Interview Answers
Why do we use multiple partitions?

Multiple partitions provide parallel processing, better throughput, and horizontal scalability.

Can consumers be more than partitions?

No. Active consumers in a consumer group cannot exceed the number of partitions.
```

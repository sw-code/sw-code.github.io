---
layout: post
title:  "transactional-outbox"
date:   2023-11-17 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, keto
image: /assets/article_images/2023-11-17-transactional-outbox/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

# Introduction: Navigating the Complexities of Modern Software Development

In the realm of software development, managing data consistency and integrity is a cornerstone of reliable systems. 
The complexity escalates when we transition from a single, well-understood system to an interconnected web of services and components.

The simplicity and robustness of SQL databases have long been a foundation in software development. 
These databases offer [ACID (Atomicity, Consistency, Isolation, Durability)](https://en.wikipedia.org/wiki/ACID) properties, ensuring transactions are processed reliably. 
In this controlled environment, developers can perform business logic within transactions confidently, knowing that any failure will trigger a rollback, preserving data integrity and preventing inconsistent states. 
This traditional model has proven effective and straightforward when operations are confined within the bounds of a single database.

However, modern software architectures often require interactions beyond a single database. 
The introduction of third-party systems and external services adds layers of complexity when business logic involves modifying data across distributed systems, 
such as a SQL database and a event bus like RabbitMQ. 
In these distributed architectures, the ACID properties, which are the backbone of transactional integrity within a single database, 
become challenging to enforce across multiple systems.

One of the core issues is the uncertainty of atomicity. 
For instance, consider a scenario where a transaction involves writing an event to a RabbitMQ and also updating a record in a database. 
There's a possibility that the event write to RabbitMQ succeeds, but the outer database transaction fails. 
This results in a scenario where an event indicating a business action is published, 
but the corresponding database state does not reflect this action because the transaction was not completed successfully. 
The system thus ends up in an inconsistent state, with the event bus reflecting a change that the database does not.

When integrating an event bus (or any other external service) within an application in a distributed system, there are two naive approaches one might consider. 
Each approach has its own set of challenges and potential pitfalls:
 
## 1. Execute Business Logic Within the Transaction and Publish Events After Committing the Transaction
In this approach, the application first executes all the required business logic within a database transaction. 
After this transaction is successfully committed, it then publishes the relevant events to the event bus.

![commit-publish](/assets/article_images/2023-11-17-transactional-outbox/commit-publish.png)

**Challenges:**

* **Risk of Event Loss:** If the application crashes or encounters an issue between the transaction commit and the event publication, the event may never get published. 
This leads to a situation where the database state has changed, but the corresponding event informing other services of this change is lost.
* **Inconsistency in Distributed Systems**: Other services relying on these events for triggering their own processes will not be aware of the changes, leading to data inconsistency across the system.
* **Complex Recovery Mechanisms**: Implementing a mechanism to check for such failures and recover from them can be complex and error-prone.

## 2. Execute Business Logic and Publish Events Within the Transaction

In this approach, the application attempts to execute business logic and publish events to the event bus, all within the same database transaction. 


![commit-publish](/assets/article_images/2023-11-17-transactional-outbox/publish-commit.png)

**Challenges:**
* **Event Publication Preceding Transaction Commit:** When events are published to the event bus as part of the database transaction, there's a risk that the transaction might not successfully commit even after the event has been sent out. 
This situation can occur due to various reasons, such as issues in completing subsequent operations within the same transaction or a late-occurring failure in the database.
* **Inconsistent State Risks:** If the transaction fails to commit after the event is published, it leads to a critical inconsistency. 
The event bus carries an event that suggests a change or action that, in reality, the database did not finalize. This discrepancy can lead to serious data integrity issues, as other services responding to the event may act on data that doesn't accurately reflect the current state of the system.
* **Complex Error Handling:** The application must be prepared to handle these scenarios, where the event is out in the world, but the corresponding transaction failed. 
This requires sophisticated error handling and potentially complex rollback mechanisms, not just for the database changes, but also for any actions taken by services that consumed the premature event.
 
  
In the past, one common approach to address such issues was through distributed transactions, like the [two-phase commit (2PC) protocol](https://en.wikipedia.org/wiki/Two-phase_commit_protocol). 
2PC was designed to ensure atomicity across multiple database systems, 
attempting to maintain data consistency by coordinating a commit or rollback across all involved systems. 
However, this approach comes with significant drawbacks, particularly in distributed and microservice-based architectures:

* **Performance Overhead**: The 2PC protocol is known for being resource-intensive and can significantly impact system performance, particularly in high-throughput environments.
* **Complexity and Scalability Issues**: Managing and coordinating distributed transactions across multiple systems adds a layer of complexity and can become a bottleneck in scalable systems.
* **Lack of Support in Modern Ecosystems**: Many modern message buses and RESTful APIs do not support the 2PC protocol, making it challenging to implement in contemporary microservices architectures.

## The Real-World Implications of Naive Approaches

From a theoretical standpoint, the challenges with these naive approaches in integrating an event bus within an application may seem obvious. 
However, drawing from my professional experience across various projects, I have observed these issues manifest repeatedly in real-world scenarios. 
Whether it's dealing with distributed systems employing message buses, interacting with external systems through REST APIs, or managing multiple databases, a common error persists: a lack of clarity among developers regarding the consistency semantics of their applications.

In practice, this oversight leads to perplexing and hard-to-diagnose errors. 
Developers often underestimate the complexity introduced by distributed environments and might not fully consider the implications of their chosen implementation strategy. 
As a result, applications can exhibit erratic behavior, where the root cause is deeply embedded in the flawed interaction between different system components. 
These issues can be especially challenging to debug and resolve, given the distributed nature of these systems and the often subtle interdependencies between their components.

The crux of the problem lies in the assumption that techniques and patterns used in monolithic or single-database applications can be directly applied to distributed systems. 
This misconception overlooks the fundamental differences in how distributed systems handle data consistency and transaction management. 
The consequences of this oversight are not just theoretical inconsistencies but tangible bugs and system failures that can have a significant impact on the reliability and integrity of the application.

This challenge highlights the inherent difficulty in ensuring atomicity and consistency when dealing with internal transactions and external communication within the same operational scope. 
It underlines the necessity for a pattern like the Transactional Outbox, which offers a way to decouple these concerns, 
ensuring that events are only published after the transaction’s successful commitment, thereby maintaining the integrity and consistency of the overall system.

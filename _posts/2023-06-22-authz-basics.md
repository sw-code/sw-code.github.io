---
layout: post
title:  "Authorization Basics"
date:   2023-06-16 22:35:25
categories: authz
tags: authentication, authorization, security
image: /assets/article_images/2023-06-17-scaling-authz-series/sansibar.jpg
author_name: Viktor Gottfried
author_image: /assets/images/authors/viktor.jpg
---

Welcome back to our [Scaling Authz]({% post_url 2023-06-17-scaling-authz-series %}) blog series, where we're documenting our journey toward a scalable and efficient authorization architecture.

# Authentication vs Authorization
Authentication and authorization, while often used interchangeably, serve different purposes within the context of security.

Authentication is the process of verifying a user's identity. It is the initial step in any secure transaction and typically involves the user providing proof of their identity in the form of credentials, such as a username and password or a digital certificate. Once the user's identity is verified, they are authenticated.

The authenticated user is then provided with a token, often in the form of a JWT (JSON Web Token). This token can include an ID token, which contains information about the user's identity (like the user's name, email, and so forth). This token is subsequently presented every time the user wants to access a specific function or service within the system.

This is where authorization comes into play. Authorization is the process that follows authentication. It's the act of determining what this authenticated user, now identified by their token, is allowed to do.

In other words, authentication answers the question, "Who are you?" while authorization answers the question, "What are you allowed to do?"

So, after a user is authenticated and their identity is established, we then use authorization to determine the boundaries of their interaction with the system's resources.

While it's possible to use information included in the authentication token, such as roles, for making authorization decisions, this approach may not scale well for complex or large systems. The roles can become too granular and numerous to manage effectively, and there's also a risk of role explosion. Instead, as we've introduced in our journey so far, we're looking for a more scalable solution. We aim to separate the authorization process from the token and move it to its own dedicated, manageable, and efficient infrastructure.

# Policy Enforcement Point (PEP) and Policy Decision Point (PDP)

![Policy Enforcement Point (PEP) and Policy Decision Point (PDP)](/assets/article_images/2023-06-22-authz-basics/pep-pdp.svg "PEP and PDP")

The authorization mechanism operates on a simple principle: it either permits or denies a user's access request. The component responsible for this final verdict is the Policy Enforcement Point (PEP). To make this decision, the PEP consults another component called the Policy Decision Point (PDP).

The PEP provides the request context to the PDP, which is crucial for the decision-making process. This context can contain various details, such as the identity of the user, the requested resource, the action the user wants to perform, and potentially other environmental or contextual attributes.

The PDP could be a part of the application or an external service. Its role is to evaluate the request context against a set of defined policies. These policies could be as simple as "user 'A' can read resource 'B'," or they can be more complex, including conditions, roles, hierarchical permissions, and more.

Upon evaluating the context against these policies, the PDP makes a decision: permit or deny. It then communicates this decision back to the PEP. The PDP doesn't enforce the decision; it merely informs the PEP of the outcome.

Finally, the PEP takes the decision from the PDP and enforces it. If the decision was 'permit', the PEP allows the request to proceed. If the decision was 'deny', the PEP blocks the request, typically returning an error message to the user such as "Access Denied."

# Separation of Concerns: Decoupling Authorization from Business Logic

Storing policies for decision-making is an integral part of the authorization process. As we emphasized in our introduction, one of our fundamental goals is to keep the business logic separate from the authorization process. Why? Because intertwining these two concerns can lead to a host of issues, including codebase complexity, reduced maintainability, and increased potential for bugs.

Instead, we advocate for storing the authorization-related data in a separate model within a dedicated persistence layer. This approach not only segregates the business logic from the authorization mechanism but also ensures that changes in either domain do not inadvertently impact the other.

Take the example of an Access Control List (ACL). An ACL is a data model that can store the necessary information for policy decisions. An entry in an ACL might include:

 * Subject - This is the entity that wishes to perform an action. Typically, it is a user identifier or a role.
 * Resource - The object on which the action is to be performed. In our model, this would be the identifier of a resource.
 * Action - The operation that the subject wishes to perform on the resource, like read, write, or delete.

Having this data in a separate ACL model allows the Policy Decision Point (PDP) to retrieve and evaluate policies independently of the business logic, aiding the maintainability and scalability of our system. Moreover, this model provides a clear, tabulated, and easily understandable visualization of our system's access control structure, further simplifying the task of managing complex authorization rules.

Indeed, while we strive for decoupling in software development to ensure modularity and scalability, some degree of coupling is inevitable, especially when it comes to something as integral as authorization.

Let's take an example: suppose you're working on a content management system. You have blog posts, and each blog post can have multiple comments. In the domain model, there are clear relationships between users, blog posts, and comments.

In the authorization model, these relationships need to be represented to ensure the correct enforcement of permissions. A user might have permission to edit their own blog post, but not someone else's. They might also have permission to delete a comment on their blog post, even if they didn't write the comment themselves.

In essence, while the authorization logic is distinct from the business logic and we aim to separate these two as much as possible, they are intrinsically linked. The domain model and the relationships within it inform the authorization model. The challenge is to design the system in a way that maintains this necessary connection without intertwining the business logic and authorization logic so much that they become difficult to manage, test, and evolve independently.

## Authorization models

Access Control Lists (ACLs) are just one way to handle authorization. Let's briefly explore some other popular models:

### Role-Based Access Control (RBAC)

As its name suggests, RBAC assigns permissions based on roles. In this model, permissions aren't directly assigned to individual users. Instead, they're associated with roles, and users are assigned these roles. RBAC makes managing permissions more straightforward, especially in large systems where a large number of users need to be managed. Instead of managing permissions for each user individually, permissions are managed at the role level, significantly reducing the complexity and administrative overhead.

### Attribute-Based Access Control (ABAC)

ABAC is an even more flexible and fine-grained access control model. In ABAC, permissions are based on attributes — characteristics or properties — of the user, the resource being accessed, and the environment.

A policy in ABAC could say something like, "Allow users with the role 'doctor' to view medical records of patients who are assigned to them, but only during office hours." As you can see, this is far more specific than what can be expressed with RBAC or ACLs.

ABAC can handle complex access control requirements, making it a good fit for highly dynamic and complex environments. However, the increased complexity can make ABAC more challenging to implement and manage compared to RBAC or ACLs.


While traditional models like ACL, RBAC, and ABAC each have their place, they may not be a perfect fit for every system. Some systems might require the simplicity of an ACL for certain resources, the role-based capabilities of an RBAC for others, and the dynamic, context-aware permissions of an ABAC for even more complex situations.

This is where the versatility of contemporary authorization frameworks shines. They are designed to combine and leverage the strengths of various models, allowing for hybrid approaches that best serve the system's requirements.

One of the prime examples of these modern, flexible authorization frameworks is [Casbin](https://casbin.org/). Casbin allows for a mix of access control models, including ACL, RBAC, and ABAC. It provides robust, customizable access control mechanisms that can be tailored to the specific needs of the application.

Casbin's primary strength lies in its policy language, which enables administrators to define complex access control rules. It does this through a highly customizable and flexible policy model structure, which is capable of supporting a combination of ACL, RBAC, and ABAC models, among others. This makes it possible to have a nuanced and fine-grained authorization model that caters to the unique requirements of your system.

Other technologies worth mentioning are [Ory Keto](https://www.ory.sh/keto/), which is inspired by [Google's Zanzibar paper](https://research.google/pubs/pub48190/) and provides a globally consistent and scalable permission system, and [Open Policy Agent (OPA)](https://www.openpolicyagent.org/), which is a general-purpose policy engine that allows for context-aware policy enforcement across the stack.

# Conclusion

Authorization is a crucial aspect of any system's security strategy. The models and mechanisms used to implement it can greatly affect the system's overall security, performance, and flexibility. In the early stages of a project, simple roles and permissions may suffice. However, as the system grows in complexity, a more refined approach becomes necessary.

Through this chapter, we hope to have shed light on some of the fundamental concepts and models in the realm of authorization. From basic models like ACL, RBAC, and ABAC to more advanced, flexible frameworks such as Casbin, Ory Keto, and OPA, we've delved into how they operate and their relative strengths and weaknesses.

Our exploration of authorization is far from over. In subsequent chapters, we'll take a closer look at modern, scalable authorization architectures, with a special focus on our experiences and lessons learned. Stay tuned!
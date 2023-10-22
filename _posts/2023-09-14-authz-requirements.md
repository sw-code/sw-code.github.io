---
layout: post
title:  "Requirements for a scalable Authorization Architecture"
date:   2023-09-14 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, oauth2
image: /assets/article_images/2023-09-14-authz-requirements/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Our prior exploration, [Authorization Basics]({% post_url 2023-08-22-authz-basics %}) of our [Scaling Authz]({% post_url 2023-06-17-scaling-authz-series %}) blog series, provided a primer on the foundational elements of authorization. Yet as organizations evolve, so too must the systems that safeguard their data and resources. This article delves deeper, defining essential criteria for an adaptive authorization system and starting our journey into their detailed exploration.

# Requirements

When designing an authorization system that scales, it's crucial to prioritize functional requirements that guarantee both performance and user satisfaction. Here are the foundational prerequisites for creating such a system.


## 1. Fine-Grained Access Management
Every individual or system component should have tailored access rights. Think of it as ensuring that each employee in a vast company has the right keycard for the specific rooms they need to enter, nothing more and nothing less. Moreover, as businesses evolve and expand, so do their requirements. What starts as a simple access model can soon become a complex web of permissions. Therefore, it's crucial to have an authorization system that not only adapts to these growing needs but is also designed to handle intricate access patterns from the outset. Building flexibility into your system from day one ensures you're not caught off guard when the business asks for a new, nuanced access rule tomorrow.

## 2. Built with Security Front and Center
Authorization, at its core, stands as a bastion against unauthorized access to critical resources. But to ensure robustness, the underlying framework employed within the authorization system becomes pivotal. Let's examine some of the foundational pillars that ensure the security of such a framework:
 * **Secure Coding Practices**: As with any software component, the framework should be developed with best coding practices to avoid common vulnerabilities such as code injection or buffer overflows. Regular security audits and adherence to guidelines like the [OWASP Top Ten](https://owasp.org/www-project-top-ten/) provide a layer of assurance.
 * **Extensibility without Exposure**: A scalable authorization architecture must allow for extensions and integrations, but each integration point poses a potential risk. The framework must support secure APIs and integrations, ensuring that extensibility doesn't equate to exposure.
 * **Strong Encryption Standards**: Data, especially concerning user roles, permissions, and policies, is critical. The chosen framework should support robust encryption both at rest and in transit, using industry-approved algorithms and protocols.
 * **Minimal Attack Surface**: The framework should be lightweight with no unnecessary components or plugins that could be exploited. This "minimalism" reduces the attack surface, making it tougher for adversaries to find vulnerabilities.
 * **Regular Patching & Updates**: Security isn't a one-time affair. As new vulnerabilities are discovered, the framework must be actively maintained and patched, ensuring that known issues are promptly addressed.
 * **Fine-Grained Auditing and Logging**: Being able to trace back actions can be a lifesaver when it comes to detecting and investigating security incidents. The framework should offer detailed auditing capabilities, capturing who did what and when.
 * **Role and Policy Lifecycle Management**: Over time, roles may become obsolete, or policies might need refining. The framework should offer mechanisms to safely deprecate roles or adjust policies, ensuring that outdated rules don't become security loopholes.

## 3. Technology Agnosticism
An effective authorization system must be a universal translator, smoothly integrating across diverse technological landscapes. It shouldn't matter whether one department uses Python and another uses Java. Authorization should be a seamless bridge.

## 4. Low Latency

Every time a user interacts with a system, there's a series of backend operations taking place, many of which are invisible to the user. Among these operations is the crucial step of verifying whether the user has the right to perform the action they're attempting. This verification, the permission check, is a constant companion to every API call, database fetch, or file access. For a seamless user experience, these checks must be lightning fast. Any delay, no matter how slight, amplifies with every interaction and request. The cumulative result is a noticeable increase in response time, which could make the difference between an efficient, enjoyable user experience and a slow, frustrating one. Thus, an authorization system must be designed with latency at the forefront, ensuring that these repeated checks don't become bottlenecks in the user's journey.

## 5. Cloud-Compatibility
In today's digital landscape, scalability is synonymous with cloud compatibility. Only cloud environments offer the elasticity and on-demand resources necessary to meet the volatile demands of a growing user base. Without a cloud-native design, the architecture would struggle to efficiently scale, both in terms of handling large numbers of users and the vast array of resources they access. It's worth noting: if your architecture doesn't need to thrive in the cloud, you probably don't need a massively scalable authorization system in the first place. Thus, designing for cloud compatibility isn't just about keeping up with technological trends; it's about laying the foundation for the scalable, high-performance system that modern applications demand.

## 6. Consistent Authorization Decisions: 
Consistency in authorization is paramount. Let's consider two contrasting scenarios:
1. A user has been restricted from accessing a sensitive resource, perhaps due to a change in their role or an observed security risk. If this revocation isn't consistently applied across all instances immediately, there's a potential window where the user can still access this data. Such inconsistencies can lead to unintentional data leaks, unauthorized actions, or breaches.
2. Conversely, imagine a user who has just been granted access to specific resources, perhaps as a result of a promotion or a new project assignment. If the system doesn't promptly recognize and enforce this updated permission, the user may face unnecessary access denial errors. This not only hampers their workflow but can also be a significant source of frustration, leading to decreased productivity and satisfaction.
In both scenarios, any change in access rights should be instantly reflected across the system, ensuring that access controls are accurate and reliable at all times, no matter where or when the access request is made.

## 7. Simplicity in Maintenance
Just like a well-curated library where one can effortlessly locate and replace books, the components of an authorization system should be designed for ease of modification, replacement, or scaling. This foresight ensures longevity, adaptability, and can lead to cost savings in the long run.

1. **Separation of Concerns**: Authorization should be managed within its own dedicated layer, distinct from the business logic. Mixing business logic with authorization checks can lead to increased complexity, making updates or changes more prone to errors. Separating these concerns ensures that modifications to one do not inadvertently affect the other.
2. **Transparent Mechanisms**: The authorization mechanism should be transparent and replicable. This ensures that, as the team grows or changes, new developers can quickly understand and work with the system without a steep learning curve.
3. **Flexibility from Business Requirements**: While the authorization system must be robust and capable of handling a myriad of scenarios, it should be independent of ever-evolving business requirements. This means that changes in business rules or strategies shouldn't necessitate a reconfiguration of the authorization architecture. The system should be adaptable enough to accommodate these changes without a need for frequent overhauls.


## 8. Scalability to Match Growth
As organizations evolve, they often witness a surge in user numbers, resources, and the frequency of requests. An authorization system should not only be comparable to a well-trained athlete, capable of sprinting when demands peak, but also maintain its performance and resilience over extended periods.

To delve deeper into what scalability truly means in this context:

1. **User Base Scalability**: The system should handle a rapid increase in users seamlessly. A tenfold or even hundredfold surge in user numbers shouldn't cause the system to falter or slow down. It should be designed such that adding more users doesn't create an exponential increase in required resources or system load.
2. **Adaptable to Expanding Business Needs**: As a company grows, it often diversifies, bringing forth new business models, services, and use cases. The authorization system should be flexible enough to accommodate this evolution. Whether there's a need for integrating new services, managing more granular permissions, or interfacing with new technologies, the system should be ready.
3. **Service Distribution**: Modern businesses, especially tech-driven ones, often adopt microservice architectures or other distributed systems. This fragmentation can pose unique challenges to authorization, as permissions may have to be checked and reconciled across various services and databases. A scalable authorization system should, therefore, be prepared to operate efficiently across such distributed environments, ensuring consistent and timely access decisions.

# 9. Testability for Peace of Mind
Ensuring the robustness of an authorization system isn't just about design – it's about validation. Testability plays a pivotal role in this:

1. **Continuous Validation**: Integrating an authorization system's test suite with an external library or service is not merely a luxury; it's a necessity. With continuous testing, you can promptly catch any regressions or unexpected behavior changes. The immediacy of feedback accelerates the development process, all the while keeping systems in check.
2. **Security Assurance**: Security is a non-negotiable aspect of authorization, and a well-crafted test suite aids immensely in this regard. By thoroughly testing the various authorization scenarios, edge cases, and potential vulnerabilities, developers can confidently make changes and enhancements. The testing framework serves as a safety net, catching any potential breaches or inconsistencies before they go live. This setup empowers developers to focus on the primary task at hand, ensuring top-tier security without getting bogged down by the nitty-gritty of each potential risk.
3. **Maintainability & Scalability**: As your system grows and evolves, so do its intricacies. A comprehensive test suite ensures that changes, no matter how minor or significant, don't inadvertently disrupt existing functionalities. This not only maintains the health of the system but also scales its resilience. Developers can swiftly integrate new features or scale existing ones, all the while having the assurance that the rest of the system remains unaffected.

# Tackling the Essentials 

In our quest to establish a solid foundation for scalable authorization, we will embark on a journey through each key requirement. These requirements aren't just academic or theoretical constructs; they represent the core of what it takes to make authorization truly effective and scalable in real-world scenarios. Our exploration begins with arguably one of the most crucial facets: Fine-Grained Access Management.

# Fine-Grained Access Management: The Heart of Modern Authorization

When discussing authorization, particularly in complex and evolving systems, the need for fine-grained access management becomes paramount. It's no longer sufficient to rely on broad strokes and generalized roles. As business requirements expand, so too does the need for a more nuanced approach to permissions. Let's delve deeper into the complexities of this and understand the nuances.

## The Limitations of Flat Roles

When we talk about flat roles, we're typically referring to systems where users are assigned broad, general roles such as "admin", "editor", or "viewer". These roles, while simple and intuitive, come with their own set of challenges when implemented within intricate organizational setups.

In real-world systems, developers often grapple with the limitations of these generalized roles by encoding information directly into the role name. A prime example would be designations like "editor:organization_a". While this might seem like a handy workaround initially, it quickly compounds the complexity of the system and detracts from the initial simplicity that flat roles promised.

These flat roles are easily understood but come with a host of limitations:

1. **Inadequate Representation of Complex Hierarchies**: In most real-world scenarios, resources and their access patterns are not flat. They follow a complex hierarchy, often with nested levels. For instance, consider a multinational corporation with departments, sub-departments, teams, and sub-teams. A flat role might easily allow access to an entire department but struggles when you need to provide access only to a specific sub-team within that department.
2. **Lack of Flexibility**: With flat roles, the permissions are typically rigid. If you need to give someone access to a resource that doesn't align neatly with these predefined roles, you either risk over-privileging them (by giving them a broader role) or under-privileging them (by denying access they might legitimately need).
3. **Maintenance Challenges**: Over time, as more exceptions and one-off permissions are added to accommodate the inadequacies of flat roles, the system becomes increasingly challenging to manage and maintain. This might lead to inefficient practices like creating a multitude of narrowly defined roles, exacerbating the issue of role explosion.

## Role Explosion

As systems aim for more granularity, there's a risk of over-complicating things. When every new business need results in a new role being created, you end up with 'role explosion'. This brings its own set of challenges:

* **Maintenance Nightmare**: With hundreds of roles, managing and updating them becomes a Herculean task. It's easy to lose track of what each role represents.
* **Decreased Security**: The more roles you have, the harder it is to ensure each is correctly configured. This could lead to security gaps.
* **Cognitive Overload**: For those managing these roles, remembering and understanding the function and scope of each becomes a challenge.
* **Token Bloat**: In flat role-based systems, especially those leaning on IDPs, roles are commonly embedded as claims within JWT tokens. Role explosion causes these tokens to become bloated. Consequently, these oversized tokens may exceed the HTTP header size limitations set by some receiving systems, rendering them non-functional due to the token's size. This leads to requests being denied, creating potential operational disruptions.

# Hierarchies and Their Importance

## Role Hierarchies
A role hierarchy can be visualized as a pyramid or tree structure, where roles inherit permissions from roles beneath them. In a hierarchical role-based system, each role can encompass the permissions of roles "below" it, making the management of permissions more modular and reducing redundancy.

**Example**: In a corporate setting, we might have:

* **Employee**: Basic access to company resources, such as the intranet or time tracking system.
* **Manager**: Inherits all permissions of the Employee role, but also has added permissions like approving timesheets or access to team performance data.
* **Director**: Inherits all permissions of both Employee and Manager roles. They might also have access to department-wide data, budgeting systems, and strategic planning tools.

In this model, if a basic permission, like accessing the company intranet, needed to be changed, you'd only need to modify it at the Employee level. Both Manager and Director roles would automatically inherit this change, thanks to the hierarchy.

## Resource Hierarchies

Just as roles can be hierarchical, so can resources. Resources are often not flat; they're nested within each other, forming a structured hierarchy. Understanding and modeling these hierarchies is essential for fine-grained access control.


Let’s take the concept of resource hierarchies and apply it to a scenario we're all familiar with: the structure of a multi-national corporation.

**GlobeCorp** is a global conglomerate with its headquarters in New York. This is our “Parent Organization”.

Under **GlobeCorp**, there are several subsidiaries:
* TechNovelties in Silicon Valley
* EcoLife in London

Now, EcoLife itself has a specialized vertical focused on organic health products named GreenHealth, based in London as well.

Imagine an employee, let’s call her Jane. Jane is an auditor and is given a 'readonly' access role to the financial data of GlobeCorp. 
Due to the hierarchy, this 'readonly' access should automatically extend to both TechNovelties and EcoLife, since they are subsidiaries of GlobeCorp.

![ReadOnly inheritance](/assets/article_images/2023-09-14-authz-requirements/global-corp-1.png)

However, Jane's role at EcoLife isn't just to audit. She's also responsible for operations. Thus, she's granted an 'admin' access role specifically for EcoLife. 
This 'admin' access, following our hierarchical structure, should grant her broader privileges not just for EcoLife, but also for its child organization, GreenHealth.

Through this example, it’s evident how roles and access permissions can cascade through a hierarchical system. 
It also underscores the importance of having a flexible authorization system that can discern between broad, inherited permissions and specific, assigned ones.

![Admin inheritance](/assets/article_images/2023-09-14-authz-requirements/global-corp-2.png)


## Exception Handling in Hierarchies

An efficient hierarchy-based system isn't merely about cascading permissions down the chain. It's equally crucial for such systems to provide the capability to make exceptions to the rule, preventing certain permissions even if the hierarchy would naturally grant them.

Let's revert back to our **GlobeCorp** scenario. **Jane**, who has a 'readonly' role at **GlobeCorp**, and an 'admin' role at **EcoLife**, might find herself in a tricky situation. Perhaps, due to new data protection regulations, she shouldn't have access to individual health records within **GreenHealth**, even though her 'admin' role for **EcoLife** would naturally grant her that access.

This scenario underscores the necessity of an authorization system that can make nuanced decisions. It should not only grant permissions based on hierarchical roles but also be adept at carving out exceptions based on specific business rules or compliance requirements. Such capabilities ensure that a company remains compliant with both its internal policies and external legal standards.

Embracing these hierarchical principles with the capacity for exception handling enables businesses to craft an authorization system that is structured yet adaptable, reflecting the real-world complexities of roles and resources.

# Conclusion & What's Next

In "Requirements for a Scalable Authorization Architecture," we've taken a deep dive into the essential requirements, both technical and functional, needed for robust authorization. One of the key components we’ve highlighted is the ever-important realm of Fine-Grained Access Management, shedding light on its complexities and indispensable role in modern systems.

However, our journey is just beginning. As we move forward, subsequent articles will delve deeper into the other requirements, ensuring you have a comprehensive understanding of what it takes to build an effective, scalable, and secure authorization system.

Stay with us for more insights and deeper explorations into the expansive world of authorization.

* [Google's Zanzibar and Beyond: A Deep Dive into Relation-based Authorization]({% post_url 2023-10-13-authz-keto-introduction %})
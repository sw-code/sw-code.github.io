---
layout: post
title:  "Future-Proofing Authorization: Leveraging OpenFGA for Enhanced Security and Scalability"
date:   2024-06-14 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, openfga
image: /assets/article_images/2024-06-14-authz-openfga-introduction/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Imagine you're an early explorer in the age of discovery, charting unknown territories where the maps of old don't suffice. 
You set sail with one set of maps, only to discover they're inadequate for navigating the new landscapes you encounter. 
Like these intrepid explorers, we too embarked on our technological voyage with Ory Keto, a map we trusted. 
But as we ventured deeper, the contours of our needs changed, revealing the limitations of our initial guide. 
We found a new chart in OpenFGA, one that promised not just to navigate the current landscapes but also to evolve with them. 
Just as explorers of old learned to adapt their charts to new worlds, we learned to adapt our tools to the evolving terrains of digital authorization.

# Introduction

While working with Ory Keto, we encountered several gaps in its feature set that could typically be addressed through active development and community support. 
However, these missing features, although critical for our project's success, were not the primary reason behind our decision to look for alternatives. 
Our main concern stemmed from the noticeable lack of activity in the [Ory Keto GitHub repository](https://github.com/ory/keto). 
Generally, an active repository is a good indicator of ongoing development, bug fixes, and a supportive community, all of which are vital signs of a project's health and future prospects. 
Unfortunately, the apparent stagnation within Ory Keto's repository suggested a shortfall in maintenance and forthcoming updates, raising doubts about its long-term viability for our needs. 
This inactivity's impact was significant; without visible updates or development, the likelihood of introducing necessary enhancements or new features diminished. 
Such stagnation not only curtails the tool's growth potential but also poses risks to the project's ability to adapt to new requirements or security concerns. 
It was this combination of factors — particularly the project's inactivity — that compelled us to seek a more reliable and actively supported solution.

However, during development, we transitioned to [OpenFGA](https://openfga.dev/) in December 2023. 
OpenFGA, which stands for Open Fine-Grained Authorization, was developed by Okta and subsequently made open-source.
Since then, we have made significant progress with our system. The implementation is complete, and we are now preparing to move into production.

## Why OpenFGA?

1. **Origin and Open Source Commitment**: Initially developed by Okta and now under the stewardship of the [Cloud Native Computing Foundation](https://www.cncf.io/), OpenFGA's open-source nature aligns perfectly with our commitment to transparent, community-driven development.
2. **Active Development and Community Engagement**: Unlike our experience with Keto, OpenFGA boasts an active development cycle. The developers are not only engaged but also readily available for support. This is further bolstered by monthly [community meetings](https://github.com/openfga/community/blob/main/community-meetings.md), accessible via Zoom and archived on [YouTube](https://www.youtube.com/playlist?list=PLUR5l-oTFZqUneyHz-h4WzaJssgxBXdxB), offering a platform for real-time interaction and knowledge sharing.
3. **Feature-Rich Offering**: OpenFGA's features are robust and well-aligned with the current demands of authorization architecture. 
4. **Enhanced Developer Experience**: One of the standout features of OpenFGA is its developer-centric approach. The [OpenFGA Playground](https://play.fga.dev/), for instance, is an exceptional tool, allowing for the writing, testing, and visualization of authorization models directly within the interface.
5. **Developer Tooling**: Catering to a vast majority of developers who use Visual Studio Code, OpenFGA offers a dedicated extension. This integration streamlines the process of working with the authorization model within a familiar coding environment. Additionally, recognizing the diverse preferences in development tools, a JetBrains plugin is also under active development as of the writing of this article, expanding support to an even broader audience within the development community. This upcoming plugin promises to bring similar benefits to those using JetBrains' suite of IDEs, further enhancing productivity and ease of access to OpenFGA’s features.
6. **Robust Testing Capabilities**: OpenFGA understands the importance of testing in software development, particularly for authorization models. The framework provides tools to write and execute tests, ensuring that the authorization models function as intended.

# OpenFGA Integration

The integration of OpenFGA into our system, guided by the principles established by [Google's Zanzibar](https://research.google/pubs/zanzibar-googles-consistent-global-authorization-system/), 
proved to be straightforward due to the underlying similarities with Ory Keto. 
Both systems utilize a model based on relation tuples, which simplifies the transition process significantly. 
We were able to seamlessly swap out our existing REST API client for one compatible with OpenFGA. 
The ease of this transition was largely due to the fundamental concepts inherited from Google Zansibar, which OpenFGA implements effectively.

## Recap of Google Zanzibar's Concepts

At its heart, Google’s Zanzibar introduces a robust yet intuitive model known as relation tuples. 
These tuples efficiently encode relationships in a structured format, facilitating complex access control scenarios. Here’s a breakdown of the components of a relation tuple:

* `tuple`: This is represented in the format `<object>#<relation>@<user>`.
* `object` Objects are expressed as `<namespace>:<objectid>`, which specifies the type and unique identifier of the object involved. 
* `relation`: Relation signifies the type of relationship between the object and the user, such as `owner` or `viewer`.
* `user`: A user can be represented directly as `userid` or as a `userset` to denote more complex user specifications.
* `userset`: This is formatted as `<object>#<relation>`, enabling definitions of relationships or groups.  

For example, a relation tuple might look like this: `document:readme#owner@user456`, meaning that `user456` is the `owner` of `readme` in the `document` namespace.
Expanding on this, the subject in a relation tuple doesn't necessarily need to be a user. It could also be another relation tuple. 
This introduces the ability to create relationships between objects, as exemplified by `document:readme#parent@folder:A`, indicating that the folder A is the parent of the document readme.


In OpenFGA, the terminology slightly varies from the Zansibar paper, where what is traditionally called a `namespace` is referred to as a `type`.

## Defining the Authorization Model in OpenFGA 

In the context of OpenFGA, an authorization model is an essential construct that consolidates one or more type definitions. 
This model serves as the backbone of the system's permission framework. 
It lays out the rules and guidelines for what types of entities exist within the system and the potential relations that can occur between these types.

By specifying types like `user` and `document`, and defining possible relationships such as `reader`, `writer`, and `owner`, the model establishes who can do what and under what circumstances. 
Each type can be seen as a category of objects, and each relationship defines how these objects can interact, ensuring that permissions are granted appropriately and securely.

Here’s how an authorization model is expressed in OpenFGA’s DSL, which highlights its role in setting up the system’s permission framework:

```
model:
    schema: 1.1

type: user

type: document
    relations:
        define reader as [user]
        define writer as [user]
        define owner as [user]
```

## Testing Capabilities in OpenFGA

OpenFGA's testing capabilities offer a significant advantage by allowing teams to validate their authorization models before integration into live applications.
This process not only enhances security and compliance but also ensures that the authorization logic performs as expected across different scenarios.

### Recap of the Scenario with Jane and GlobeCorp

Previously, we discussed the scenario involving Jane, an auditor at GlobeCorp. 
Jane had 'readonly' access to GlobeCorp's financial data and also held an admin role at EcoLife. 
This scenario sets the stage for testing complex permission structures to ensure they function correctly within the system.

![Relations](/assets/article_images/2024-06-14-authz-openfga-introduction/global-corp-2.png)

### Defining the Authorization Model

To encapsulate Jane's permissions within GlobeCorp, we crafted an authorization model. Here's how it is defined in OpenFGA's format:

```
model:
  schema: 1.1

type: user

type: organization
  relations:
    define owner as [user] or owner from parent
    define parent as [organization]
    define editor as [user] or editor from parent or owner
    define viewer as [user] or viewer from parent or owner

    define can_view as viewer
    define can_edit as editor
```

This model specifies hierarchical permissions, reflecting Jane's access levels across different organizational layers.

### Testing the Model

Tests in OpenFGA are specified in a YAML format, which makes them easily readable and maintainable. 
For Jane's scenario, we could define a test to verify that she has the correct `can_view` permissions for GlobeCorp's data and the `can_edit` permissions for EcoLife.

Here’s an example of how such a test might look:

```yaml
name: demo
model_file: ./model.fga
# Sets up the initial state by defining specific relationships
tuples:
  - user: organization:GlobalCorp
    relation: parent
    object: organization:EcoLIfe
  - user: user:Jane
    relation: viewer
    object: organization:GlobalCorp
  - user: user:Jane
    relation: editor
    object:  organization:EcoLIfe

tests:
  - name: Jane has limited access
    check:
      - user: user:Jane
        object: organization:GlobalCorp
        assertions:
          can_view: true
          can_edit: false
      - user: user:Jane
        object: organization:EcoLIfe
        assertions:
          can_view: true
          can_edit: true

```

To execute the tests, you utilize the OpenFGA CLI with the following command:

```bash
fga model test --tests .\tests.fga.yml
```

These tests explicitly verify whether the authorization model correctly interprets Jane's permissions as intended. 
By running these tests, teams can proactively identify and rectify issues, ensuring the model's accuracy before it impacts the live environment.

This structured approach to defining and testing authorization models within OpenFGA not only streamlines development workflows but also significantly boosts confidence in the security and correctness of the implemented permissions system.

## OpenFGA Server: Configuration and Operations

OpenFGA introduces a more dynamic and flexible approach to managing authorization models compared to Ory Keto. 
While Ory Keto integrates the authorization model as a fixed configuration during startup, 
OpenFGA enhances operational flexibility by supporting multiple authorization models, each housed within its own distinct storage environment, referred to as a `store`.

A store in OpenFGA acts as an encapsulation layer, designed to segment and manage different sets of authorization models. 
This architecture is particularly advantageous for organizations that operate across multiple teams or development stages. 
Each store can contain several authorization models, allowing for tailored configurations suited to specific team requirements or project phases.

One of the critical advantages of this setup is the ability to version authorization models effectively. 
For instance, when a new model version is rolled out, it does not necessitate an immediate migration by all services. 
Instead, services can continue to operate using older, well-tested versions of the model until they are ready to transition. 
This capability is crucial for maintaining stability and continuity in large-scale environments where sudden changes can disrupt operations.

The creation and management of stores and models are primarily conducted through OpenFGA’s API. This design allows for high flexibility in how these elements are set up and modified:

* `OpenFGA CLI`: This tool is an integral part of the OpenFGA ecosystem and can be used to configure stores and models within a CI pipeline. By scripting and automating these tasks, teams can seamlessly integrate authorization model updates into their deployment processes, ensuring that changes are rolled out efficiently and consistently.
* `REST Client`: For more dynamic operational needs, using a REST client allows teams to manage authorization models directly through API calls. In our setup, we utilize a central authorization service that leverages this capability to adjust models on the fly. This method is particularly useful for environments where authorization needs can change rapidly, requiring quick adaptations without redeploying or restarting services.

### Operational Challenges

OpenFGA brings a highly flexible and scalable approach to managing authorization models. 
However, with its innovative features, certain operational challenges emerge that could impact deployment and maintenance strategies. 
Here's an exploration of some of these challenges and the measures we've taken to address them.

**Non-Unique Store Names**

One of the first challenges we encountered involves the creation of stores in OpenFGA. 
While the platform allows for the creation of stores by name, it treats these names merely as display labels rather than unique identifiers. 
Upon creation, each store is assigned a unique ID by OpenFGA, which is used to reference the store programmatically. 
However, the non-uniqueness of store names can lead to potential confusion or errors, particularly if a store is inadvertently created multiple times under the same name. 
This issue has been recognized within the community and is currently under discussion in open issues on OpenFGA’s GitHub repository: [#1132](https://github.com/openfga/openfga/issues/1132), [#989](https://github.com/openfga/openfga/issues/989).

**Challenges with Authorization Model IDs**

Another significant challenge is the management of Authorization Model IDs. 
In OpenFGA, each model's ID is unknown until the model is created, and there's no way to set a predictable or semantic ID, such as a tag. 
This lack of control over model IDs complicates the process of aligning and referencing the correct authorization model across multiple services.

To mitigate this, we have developed a workaround: each service deployment includes a resource file specifying the used authorization model. 
When a service starts, it compares the model detailed in its resource file against all models available on the OpenFGA server by iterating over them to find a match. 
Although this method is currently sufficient, it is less than ideal, particularly at scale or in dynamic environments where models might frequently change.

# SpringBoot Integration with OpenFGA

In keeping with our tradition of providing practical examples, we have set up a SpringBoot integration with OpenFGA, which can be accessed in our GitHub repository. 
This example serves as a straightforward guide for those looking to integrate OpenFGA into their SpringBoot applications.

The complete example with the SpringBoot integration is available at: [AuthZ OpenFGA](https://github.com/sw-code/swcode-samples/tree/main/authz/authz-openfga)

While we have extensively discussed the integration details in our previous articles with [Ory Keto]({% post_url 2023-10-13-authz-keto-introduction %}), 
this example primarily focuses on swapping the Ory Keto API client with the OpenFGA client. 
This change is straightforward due to the similarities in their relation-based authorization models which both systems derive from Google Zanzibar.

The setup process remains consistent with our previous examples. 
The project includes a docker-compose file that sets up the necessary infrastructure components, such as PostgreSQL, Keycloak, and OpenFGA.

Upon starting the application, a specific Docker container is tasked with creating the authorization model in OpenFGA. 
This setup ensures that everything needed to authenticate and authorize users is operational right from the get-go, allowing you to focus on the specific behaviors of your application rather than the underlying authentication and authorization mechanisms.

## Why We Won't Dive Into Implementation Details
Given the structural similarity to our previous example with [Ory Keto]({% post_url 2023-10-13-authz-keto-introduction %}), 
we have chosen not to delve into the specifics of the codebase again. 
The primary modification — replacing the Keto API client with the OpenFGA client — is minimal and straightforward, thus not necessitating a detailed walkthrough.

This example aims to provide a clear and functional integration of OpenFGA in a SpringBoot environment, illustrating the ease with which OpenFGA can be incorporated into existing systems. 
We encourage you to explore the example, modify it, and adapt it to your specific needs, leveraging OpenFGA's powerful capabilities for your applications.

## Spring Boot Starter

It's worthwhile to explore the [OpenFGA Spring Boot Starter](https://github.com/openfga/spring-boot-starter). 
While we haven't used it ourselves due to our primary use of Ktor, we offer Spring Boot examples to ensure wider accessibility, given its familiarity to many developers.

# Conclusion and Looking Ahead

As we conclude this exploration of integrating OpenFGA with Spring Boot, we've touched upon the foundational aspects and benefits of using OpenFGA in scalable authorization architectures. 
The transition from Ory Keto to OpenFGA has opened new avenues for enhancing our system's robustness and flexibility, addressing the evolving needs of our project.

Looking forward, as we move into production, we anticipate encountering new issues and challenges. 
These experiences will provide valuable lessons that we look forward to solving and sharing with you. 
Additionally, we plan to delve deeper into more complex challenges associated with implementing advanced authorization features, 
particularly filtering and pagination in the context of authorization. 
These areas present unique difficulties as they require integrating detailed permission checks with database queries to ensure both efficiency and security.

Stay tuned as we continue to explore the cutting-edge of authorization technology, aiming to provide practical guidance and advanced strategies to enhance your applications' security and usability.
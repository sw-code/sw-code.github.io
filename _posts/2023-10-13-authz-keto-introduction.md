---
layout: post
title:  "Exploring Relation-based Models"
date:   2023-10-13 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, keto
image: /assets/article_images/2023-09-14-authz-requirements/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Dive into the world of online permissions and it quickly becomes clear: it's all about the connections you hold. Imagine being at an upscale gala. It's not just about your invitation card that gets you through the door. It's about who you know inside, the conversations you're a part of, and the circles you move in.

In the digital realm, it's similar. Access isn't always about direct permissions; it's about the intricate web of relationships that you're entangled in. Can you view this document? Well, it depends – are you linked to its creator, or part of the team that worked on it?

It's this nuanced dance of relationships that defines relation-based authorization. Less of a straightforward checklist and more of a dynamic network of ties, it offers a richer, deeper layer to deciding "Who gets to do what?".

# The Google Zanzibar Paper and its Influence on Rapid Authorization Framework Development

In recent years, a notable event has had a profound influence on the world of authorization frameworks. This event was the release of the [Google Zanzibar paper](https://research.google/pubs/pub48190/), a highly insightful document that details Google's approach to scalable, efficient authorization.

Google's Zanzibar system is a testament to the prowess of modern technology. According to the paper, Zanzibar handles access management for an astonishing number of resources - we're talking trillions of objects - used by hundreds of millions of users. What makes these figures even more impressive is the wide array of client services that rely on Zanzibar. Renowned platforms like Google Maps, Google Drive, and YouTube, all depend on Zanzibar for their authorization needs.

But Zanzibar doesn't just handle this enormous load, it does so rapidly and reliably. The system boasts a remarkable response time, with 95% of requests being handled within just 10 milliseconds. On top of this, Zanzibar has proven its reliability, maintaining an exceptional availability rate of 99.999% over the past three years.

However, the impact of the Zanzibar paper extends beyond these impressive technical capabilities. The release of the paper pushed a flurry of development in the authorization realm, resulting in a variety of open-source projects and SaaS offerings inspired by Google's Zanzibar, including [Ory Keto](https://github.com/ory/keto), [AuthZed](https://authzed.com/), and [OpenFGA](https://openfga.dev/).

But why has this paper sparked such a wave of development? The answer lies in a combination of factors:

1. **Scalability**: As mentioned, Zanzibar's ability to handle authorization for trillions of objects by hundreds of millions of users is awe-inspiring. As more businesses adopt microservices and cloud-native architectures, the need for scalable authorization solutions becomes increasingly pressing. Zanzibar offers a proven blueprint for achieving such scalability.
2. **Flexibility**: The Zanzibar model, termed 'Global, Consistent Authorization System', is not tied to specific business logic or policies. This flexibility makes it broadly applicable across various domains and use cases, inspiring many to leverage it in their contexts.
3. **Efficiency**: Efficiency in authorization is crucial. Zanzibar's use of data sharding and re-sharding for optimal use of resources has been an inspiration for many developing authorization frameworks.
4. **Reliability**: Google's paper emphasizes Zanzibar's ability to provide consistent authorization decisions, even in the face of network partitions and system failures. This high level of reliability is a key factor in Zanzibar's appeal.
5. **Innovation**: Finally, the Zanzibar paper represents a significant leap in the way we think about and implement authorization. Its innovative approach to handling access control at a massive scale has piqued the interest of many in the field, spurring them to explore and adopt similar strategies.

The widespread interest and rapid development sparked by the Zanzibar paper are testaments to its game-changing potential in the realm of scalable authorization.
As we at SWCode embark on our journey to implement a scalable authorization architecture, we too are inspired by the lessons gleaned from Google's Zanzibar.

## Unpacking Zanzibar's Relation-Tuple-Based Model

At the core of Google's Zanzibar system is a surprisingly simple yet powerful model: relation tuples. A relation tuple is a small record that describes a relationship, in the form of `namespace:object#relation@subject`. In this format:

* `namespace` represents the type of the object, such as 'document' or 'folder'.
* `object` refers to a unique identifier for a specific instance of that type, like a specific document ID or folder ID.
* `relation` relation signifies the type of relationship between the object and the subject, such as 'owner' or 'viewer'.
* `subject` can either be a user or another relation tuple, allowing for complex, nested relationships.

For example, a relation tuple might look like this: `document:readme#owner@user456`, meaning that `user456` is the `owner` of `readme` in the `document` namespace.
Expanding on this, the subject in a relation tuple doesn't necessarily need to be a user. It could also be another relation tuple. This introduces the ability to create relationships between objects, as exemplified by `document:readme#parent@folder:A`, indicating that the folder A is the parent of the document readme.

By combining these simple elements, Zanzibar can describe complex hierarchical relationships and permissions across vast numbers of objects and users. This granularity of control is one of the key reasons for Zanzibar's flexibility and scalability, and it's a fundamental concept that has been adopted by Zanzibar-inspired frameworks mentioned before.

Expanding on this concept, Zanzibar's relationships weave a structured graph connecting objects. Its strength lies in the straightforward approach: for authorization validation, Zanzibar merely verifies the presence of a certain relationship. Using our previous example, it would simply verify if there's an `owner` connection between the document `readme` and `user456`. To dive deeper into this mechanism and gain a clearer understanding, continue to our next chapter where we dissect the Ory Keto framework, a system inspired by Zanzibar.

It's worth noting that this explanation is somewhat simplified for clarity. The original Zanzibar paper is a scientific work and delves into greater theoretical depth, but the above should provide readers with a foundational understanding of the core concept.

# Ory Keto

Ory Keto, written in Go, is an open-source authorization system that pivots around the concept of relation tuples, echoing the foundational principles of Google's Zanzibar. Ory Keto is packaged as a standalone service, offering both HTTPS and gRPC APIs for seamless integration across diverse systems. Recognizing the varied deployment needs of organizations, Ory provides Keto both as a Software as a Service (SaaS) offering and a self-hosted solution, ensuring adaptability across different infrastructural requirements.

While conceptualizing Ory Keto, certain guiding principles and assumptions were maintained to ensure its efficacy:

* **Simplicity in Management**: The system was built on the hypothesis that no individual or team would dedicate their full time to configuring an authorization system.
* **Intuitive Nature**: Keto is designed to be self-explanatory, minimizing the learning curve and ensuring that users find the system approachable.
* **Familiarity**: By adhering to familiar paradigms and practices, Keto reduces the onboarding time for developers and system administrators.
* **Ease of Modification**: Any changes or updates to the authorization configurations can be carried out with high confidence, ensuring system stability.
* **Editorial Support**: Thanks to the Ory Permission Language, which is a subset of Typescript, Keto is compatible with numerous editors. This ensures users have the flexibility and familiarity in their choice of tools.

## Core Concepts of Ory Keto

1. **Adoption of the Relation Tuple Model**: Ory Keto adapts the Zanzibar-inspired relation tuples model. As we’ve previously elaborated, a relation tuple succinctly describes a relationship in a format like `namespace:object#relation@subject`. Through these tuples, Ory Keto can express and evaluate intricate hierarchical relationships and permissions across a multitude of objects and users, ensuring a granular and fine-tuned access control mechanism.
2. **Ory Permission Language (OPL)**: To complement its adoption of relation tuples, Ory Keto introduces the Ory Permission Language (OPL). Conceptualized as a subset of TypeScript, OPL facilitates the definition and evaluation of policies and permissions within the Keto environment. By leveraging a familiar syntax style and structure, OPL provides developers with a more intuitive and streamlined process to articulate complex authorization policies, while maintaining efficiency and precision.

### Ory Keto Namespaces

In Ory Keto, namespaces are essentially the building blocks that aid in grouping related objects. To define these namespaces, Ory Keto uses TypeScript's class structures. 

```
import { Namespace, Context } from "@ory/keto-namespace-types"

class User implements Namespace {}
class Document implements Namespace {}
class Folder implements Namespace {}
```

### Ory Keto Relations

In Ory Keto, defining relations is at the heart of the model. Let's illustrate this with a hands-on example featuring two namespaces: Document and User.
Within the Document namespace, a user is designated as either an owner, a viewer, or an editor of a document through specific relations.

The core of authorization revolves around permissions, such as 'view' or 'edit', rather than checking relations like 'owner' or 'editor'. The concrete permission is checked against the relationships. Within the Ory Permission Language, permissions are declared as functions inside the permits property of the corresponding namespace. Let's explore how permissions might be outlined for our Document namespace:

```
class Document implements Namespace {
  related: {
    owners: User[]
    editors: User[]
    viewers: User[]
  }

  permits = {
    view: (ctx: Context): boolean =>
      this.related.viewers.includes(ctx.subject) ||
      this.related.editors.includes(ctx.subject) ||
      this.related.owners.includes(ctx.subject)
  }  
}
```

## Revisiting Jane with Ory Keto Relations


In our preceding discussions on Role-Based Access Control (RBAC), we introduced Jane, an auditor working for GlobeCorp. Jane was granted 'readonly' access to GlobeCorp's financial data and held the admin role at EcoLife. This time around, let's reframe Jane’s scenario using Ory Keto’s relation tuple framework.

![RBAC](/assets/article_images/2023-09-14-authz-requirements/global-corp-2.png)

Here's a quick recap:
* GlobeCorp is a global conglomerate
* TechNovelties and EcoLife are direct subsidiaries of GlobeCorp
* GreenHealth is a specialized vertical of EcoLife

In the OPL, all organizations will be clustered within a singular 'Organization' namespace, with functional dependencies illustrated using the 'parents' relations.

```
class Organization implements Namespace {
  related: {
    editors: User[]
    viewers: User[]
    parents: Organization[]
  }

  permits = {
    view: (ctx: Context): boolean =>
      this.related.viewers.includes(ctx.subject) ||
      this.related.editors.includes(ctx.subject) ||
      this.related.parents.traverse((p) => p.permits.view(ctx))
    edit: (ctx: Context): boolean =>
      this.related.editors.includes(ctx.subject) ||
      this.related.parents.traverse((p) => p.permits.edit(ctx))      
  }  
}
```

The diagram effectively visualizes Jane's direct relations and how they cascade down the organization hierarchy using the parents relation in the context of Ory Keto's permission system.

![Relations](/assets/article_images/2023-10-13-authz-keto-introduction/opl.png)

Given this arrangement and the provided OPL:

* Jane has a "viewer" relation to GlobeCorp, allowing her 'readonly' access to its data. This relationship extends hierarchically, meaning Jane can also view data of the companies that are subsidiaries or children of GlobeCorp due to the parents relation, which are TechNovelties and EcoLife.
* Jane, due to her 'editors' relation with EcoLife, is endowed with edit access to its data. Leveraging the hierarchical structure, this access naturally extends to GreenHealth, EcoLife's subsidiary.

# A Hands-on Integration of Ory Keto and Spring Boot

Alright, it's action time! You've gotten a taste of what Ory Keto can do. Now, let's see it in action with a practical Spring Boot application that showcases our fictional organization's structure.

Here's the Game Plan:

* Setup Fun: We'll get everything in order, ensuring our environment is ready for some Ory Keto magic.
* Integration Time: We'll mesh Ory Keto with our Spring Boot app. It's like pairing wine and cheese, but for developers.
* Play Around: Ever wanted to see how policies play out in real-time? This is your chance. Tweak, test, and explore the effects of different access control configurations.
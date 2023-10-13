---
layout: post
title:  "Keto Introduction"
date:   2023-10-13 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, keto
image: /assets/article_images/2023-09-14-authz-requirements/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

# The Google Zanzibar Paper and its Influence on Rapid Authorization Framework Development

In recent years, a notable event has had a profound influence on the world of authorization frameworks. This event was the release of the [Google Zanzibar paper](https://research.google/pubs/pub48190/), a highly insightful document that details Google's approach to scalable, efficient authorization.

Google's Zanzibar system is a testament to the prowess of modern technology. According to the paper, Zanzibar handles access management for an astonishing number of resources - we're talking trillions of objects - used by hundreds of millions of users. What makes these figures even more impressive is the wide array of client services that rely on Zanzibar. Renowned platforms like Google Maps, Google Drive, and YouTube, all depend on Zanzibar for their authorization needs.

But Zanzibar doesn't just handle this enormous load, it does so rapidly and reliably. The system boasts a remarkable response time, with 95% of requests being handled within just 10 milliseconds. On top of this, Zanzibar has proven its reliability, maintaining an exceptional availability rate of 99.999% over the past three years.

However, the impact of the Zanzibar paper extends beyond these impressive technical capabilities. The release of the paper heralded a flurry of development in the authorization realm, resulting in a variety of open-source projects and SaaS offerings inspired by Google's Zanzibar, including [Ory Keto](https://github.com/ory/keto), [AuthZed](https://authzed.com/), and [OpenFGA](https://openfga.dev/).

But why has this paper sparked such a wave of development? The answer lies in a combination of factors:

1. **Scalability**: As mentioned, Zanzibar's ability to handle authorization for trillions of objects by hundreds of millions of users is awe-inspiring. As more businesses adopt microservices and cloud-native architectures, the need for scalable authorization solutions becomes increasingly pressing. Zanzibar offers a proven blueprint for achieving such scalability.
2. **Flexibility**: The Zanzibar model, termed 'Global, Consistent Authorization System', is not tied to specific business logic or policies. This flexibility makes it broadly applicable across various domains and use cases, inspiring many to leverage it in their contexts.
3. **Efficiency**: Efficiency in authorization is crucial. Zanzibar's use of data sharding and re-sharding for optimal use of resources has been an inspiration for many developing authorization frameworks.
4. **Reliability**: Google's paper emphasizes Zanzibar's ability to provide consistent authorization decisions, even in the face of network partitions and system failures. This high level of reliability is a key factor in Zanzibar's appeal.
5. **Innovation**: Finally, the Zanzibar paper represents a significant leap in the way we think about and implement authorization. Its innovative approach to handling access control at a massive scale has piqued the interest of many in the field, spurring them to explore and adopt similar strategies.

The widespread interest and rapid development sparked by the Zanzibar paper are testaments to its game-changing potential in the realm of scalable authorization.
As we at SWCode embark on our journey to implement a scalable authorization architecture, we too are inspired by the lessons gleaned from Google's Zanzibar.

## Unpacking Zanzibar's Relation-Tuple-Based Model

At the core of Google's Zanzibar system is a surprisingly simple yet powerful model: relation tuples. A relation tuple is a small record that describes a relationship, in the form of namespace:object#relation:subject. In this format:

* `namespace` represents the type of the object, such as 'document' or 'folder'.
* `object` refers to a unique identifier for a specific instance of that type, like a specific document ID or folder ID.
* `relation` relation signifies the type of relationship between the object and the subject, such as 'owner' or 'viewer'.
* `subject` can either be a user or another relation tuple, allowing for complex, nested relationships.

For example, a relation tuple might look like this: document:readme#owner@user456, meaning that 'user456' is the 'owner' of 'readme' in the 'document' namespace.
Expanding on this, the subject in a relation tuple doesn't necessarily need to be a user. It could also be another relation tuple. This introduces the ability to create relationships between objects, as exemplified by document:readme#parent@folder:A, indicating that the folder A is the parent of the document readme.

By combining these simple elements, Zanzibar can describe complex hierarchical relationships and permissions across vast numbers of objects and users. This granularity of control is one of the key reasons for Zanzibar's flexibility and scalability, and it's a fundamental concept that has been adopted by Zanzibar-inspired frameworks mentioned before.

Expanding on this concept, Zanzibar's relationships weave a structured graph connecting objects. Its strength lies in the straightforward approach: for authorization validation, Zanzibar merely verifies the presence of a certain relationship. Using our previous example, it would simply verify if there's an 'owner' connection between the document 'readme' and 'user456'. To dive deeper into this mechanism and gain a clearer understanding, continue to our next chapter where we dissect the Ory Keto framework, a system inspired by Zanzibar.

# Ory Keto

Ory Keto, written in Go, is an open-source authorization system that pivots around the concept of relation tuples, echoing the foundational principles of Google's Zanzibar. Ory Keto is packaged as a standalone service, offering both HTTPS and gRPC APIs for seamless integration across diverse systems. Recognizing the varied deployment needs of organizations, Ory provides Keto both as a Software as a Service (SaaS) offering and a self-hosted solution, ensuring adaptability across different infrastructural requirements.

## Core Concepts of Ory Keto

1. **Adoption of the Relation Tuple Model**: Ory Keto adapts the Zanzibar-inspired relation tuples model. As we’ve previously elaborated, a relation tuple succinctly describes a relationship in a format like `namespace:object#relation:subject`. Through these tuples, Ory Keto can express and evaluate intricate hierarchical relationships and permissions across a multitude of objects and users, ensuring a granular and fine-tuned access control mechanism.
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

```
class User implements Namespace {}

class Document implements Namespace {
  related: {
    owners: User[]
    editors: User[]
    viewers: User[]
  }
}
```

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

Here, the permits method establishes which relations ('owner', 'viewer', 'editor') are sanctioned to carry out certain actions ('view', 'edit').

When a request is made to verify a specific action, Ory Keto will consult the relationships database. For instance, if there's an inquiry to confirm a 'view' permission for a document, Keto will verify if there's a recognized 'owner', 'editor', or 'viewer' relationship between that document and the querying user in the relationships database.

Building on our prior illustration, if we're assessing whether user456 can 'view' readme, and our relationships indicate that User456 is an 'owner' of readme, the check would consequently be validated, since owners inherently possess the 'view' permission.

Building upon our existing example, let's introduce the Folder namespace to illustrate the relations between objects:

```
class Document implements Namespace {
  related: {
    owners: User[]
    editors: User[]
    viewers: User[]
    parents: Folder[]
  }

  permits = {
    view: (ctx: Context): boolean =>
      this.related.viewers.includes(ctx.subject) ||
      this.permits.edit(ctx),
    edit: (ctx: Context): boolean =>
      this.related.editors.includes(ctx.subject) ||
      this.permits.share(ctx),
    delete: (ctx: Context): boolean =>
      this.permits.share(ctx),
    share: (ctx: Context): boolean =>
      this.related.owners.includes(ctx.subject) || this.related.parents.traverse((parent) => parent.permits.share(ctx)),
  }
}

class Folder implements Namespace {
  related: {
    owners: User[]
    editors: User[]
    viewers: User[]
    parents: Folder[]
  }

  permits = {
    delete: (ctx: Context): boolean => this.permits.share(ctx),
    share: (ctx: Context): boolean =>
      this.related.owners.includes(ctx.subject) || 
      this.related.parents.traverse((parent) => parent.permits.share(ctx)),
  }
}
```

In the Document namespace, we've added the parents relation to Folders.

The Folder namespace incorporates the parents relationships. The parents array captures the notion of nested folders, enabling the representation of a folder structure wherein a folder can be a child of another folder. 

**Permissions**:
* **Delete**: The folder/docuement can be deleted only by those who can share it.
* **Share**: The sharing privilege is exclusive to owners and those who've received permissions from parent folders.

The provided permission model mirrors the familiar file system structure seen in widely-used platforms such as Google Drive or OneDrive.

It's crucial to discern that these relationships are not direct permissions themselves. Instead, they set the stage for determining permissions based on the web of relationships. This distinction is fundamental. By decoupling relationships from permissions, the system gains significant flexibility. One can easily change, expand, or restrict permissions without overhauling the foundational relationships. This makes it adaptable and scalable, allowing organizations to cater the system to their ever-evolving needs.
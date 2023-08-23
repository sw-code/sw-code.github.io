---
layout: post
title:  "Authorization Basics"
date:   2023-07-23 12:35:25
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, oauth2
image: /assets/article_images/2023-08-22-authz-basics/basics.jpg
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Welcome back to our [Scaling Authz]({% post_url 2023-06-17-scaling-authz-series %}) blog series, where we're documenting our journey toward a scalable and efficient authorization architecture.

In this second installment of our blog series, we'll delve into the foundational aspects of authorization. We'll explore various models from simple authentication-based authorization, through role-based access control, and up to complex relationship-based models.

To ensure a hands-on experience, each segment of our series will be accompanied by practical examples and implementation snippets. For those enthusiastic about diving deeper, all resources, code samples, and detailed walkthroughs are readily available in our [dedicated repository](https://github.com/sw-code/swcode-samples). We believe that the synergy of theoretical knowledge and hands-on practice paves the way for profound understanding and mastery.


# Authentication vs Authorization

Authentication: Think of it as the grand entrance—the main gateway to your application. It's about ascertaining "who" stands at the gates. Whether you employ the age-old username-password duo, modern techniques like OAuth, or even the increasingly popular OpenID Connect (often recognized in functionalities like "Login with Google"), the essence remains the same: confirming the identity of the entity trying to gain access. 

Authorization: Once past the main gates, what parts of the castle is one permitted to explore? That's the realm of authorization. It delineates what you're allowed to do once you've made your entrance. The rooms you can enter, the secrets you can access, all fall under its domain.

In other words, authentication answers the question, "Who are you?" while authorization answers the question, "What are you allowed to do?"

In real-world applications, the distinctions between authentication and authorization begin to blur as they work hand-in-hand. Once a user's identity is verified, this information is frequently used to determine the permissions they have. For instance, after successfully authenticating, a system might use the user's ID or other attributes to determine which resources or actions they can access.

This series will primarily center on the nuances of authorization. However, we'll touch upon authentication when it's relevant to the topic at hand. We assume readers are already familiar with, or have in place, a basic authentication system, and there's a bunch of resources available for those looking to further delve into authentication mechanisms.

# Authentication as Authorization

The simplest form of authorization is when the mere identity of a user is sufficient to grant access to resources. Such scenarios commonly surface in systems engineered for limited functionality or catering to a compact user base. In these instances, user authentication effectively doubles as a form of authorization.

## A Glimpse into the Process

![simplified authetication flow](/assets/article_images/2023-08-22-authz-basics/oauth2-simle.svg)

1. User Accesses Web Application: A user attempts to access a protected resource or service within a web application.
2. Authentication Required: Recognizing that the user is not yet authenticated, the application redirects the user to a login page.
3. User Authenticates: The user provides their credentials, such as a username and password.
4. Token Granted: Upon successful authentication, the identity provider issues a token to the user. This token acts as a proof of the user's identity and might contain claims, roles, or other pertinent information.
5. Token Utilized: The web application uses this token to grant the user access to its services and to communicate with backend APIs. It is essential to note that the backend verifies the integrity of this token to ensure it's genuine and hasn't been tampered with.


The flow above is reminiscent of the OAuth2 "Authorization Code Flow", albeit in a simplified manner. It primarily aims to illustrate the core concept of authentication. For those looking for an in-depth dive into OAuth2, consider exploring [Auth0's comprehensive guide on the Authorization Code Flow](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow).

# Authorization Models

In the intricate world of authorization, one size seldom fits all. As applications grow and requirements become more nuanced, the mechanisms to decide who gets to do what need to evolve as well. The diversity of use-cases and the need for granularity have given rise to various models of authorization. These models serve as conceptual frameworks, providing structured methods to grant or deny access to resources based on different criteria. From the rudimentary setups that hinged primarily on lists to the more intricate designs that account for dynamic attributes and relationships, the evolution of these models mirrors the increasing complexity of our digital ecosystems.

Let's dive deeper into some of the prevailing models in authorization:

* ACL (Access Control List): Stemming from its historical roots in the Unix file system, ACLs provide a list of permissions attached to an object. They are inherently a means to define which user have access to specific objects and the operations they can perform on them.

* RBAC (Role-Based Access Control): A more structured approach, RBAC assigns permissions to specific roles rather than individual users. Users are then assigned to roles. This model offers simplicity and scalability, especially in larger systems where defining permissions for each user can become unwieldy.

* ABAC (Attribute-Based Access Control): A more dynamic approach, ABAC bases access decisions on policies derived from various attributes—be it of the user, the resource, or even the surrounding environment. For instance, a document might only be accessible during working hours or if a user is located in a specific country. This approach provides nuanced, context-aware authorization.

* ReBAC (Relationship-Based Access Control): Building on ABAC, ReBAC incorporates relationships. Access decisions take into account both attributes and their interrelationships. Examples include:
    * Users can delete comments they are the **owner** of.
    * Reading an issue is permissible if you are a contributor to its **parent** repository.
    * One becomes a repository contributor if they are **member** of of a team with the "contributor" role.

While each model brings its distinct approach, in practice, they often intermingle. Implementations commonly weave elements from multiple models to create a tailored authorization mechanism that comprehensively fulfills specific requirements. The blending of these models in real-world setups reflects the flexibility and complexity required in today's digital ecosystems.

# Hands-On

Diving into the practicalities, we'll use a Spring Boot application to breathe life into our conceptual understanding of authorization. Our canvas for this exercise is a modest web service dedicated to managing a list of pets. This service, while simple, will help us demarcate the boundaries between an authenticated user and one that comes adorned with specific roles or permissions.

For the scope of this chapter, our emphasis will mainly be on **authentication as authorization**" and on the facets of **Role-Based Access Control (RBAC)**. As we progress further in the series, we'll delve deeper into intricate authorization mechanisms, but for now, these two paradigms serve as our focal points.
 
## The Pet Controller

Consider this Spring Boot PetsController. It offers two endpoints:

1. An endpoint to fetch a list of pets, accessible by any authenticated user.
2. An endpoint to add a new pet, restricted to only those users with the 'admin' role.

```kotlin
@RestController
class PetsController {
    private val pets = mutableListOf(Pet("dog"))

    @GetMapping("/pets")
    fun getPets(): List<Pet> {
        return pets
    }

    @PostMapping("/pets")
    @PreAuthorize("hasAnyRole('admin')")
    fun addPet(pet: Pet): Pet {
        pets.add(pet)
        return pet
    }
}

data class Pet(val species: String)
```

In the code above, the @GetMapping annotation is standard for any Spring Boot application, denoting an HTTP GET endpoint. However, the @PostMapping is a bit special. It's decorated with the @PreAuthorize annotation, a component of Spring Security, which states that the following method (in this case, addPet) can only be accessed if the authenticated user has an 'admin' role.

When a user attempts to post a new pet to our service, the @PreAuthorize annotation intercepts the request and evaluates the user's roles. If they have the 'admin' role, the method is executed. Otherwise, an access denied response is returned.

This example showcases a simplified form of Role-Based Access Control (RBAC). Here, roles embedded within the user's token (as discussed in the previous chapter) are evaluated by the Spring framework to make authorization decisions.


# Upcoming

As we journey through this series, we'll delve deeper into more intricate mechanisms with practical exercises, especially Relationship-Based Access Control (ReBAC). This blog is largely centered on the latter, given our keen interest in harnessing the capabilities of [Google's Zanzibar](https://research.google/pubs/pub48190/), a powerful system known for its robust ReBAC features. By familiarizing ourselves with these advanced techniques, we aim to demonstrate the potent potential and flexibility that advanced authorization mechanisms can bring to contemporary applications.
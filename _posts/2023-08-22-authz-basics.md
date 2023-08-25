---
layout: post
title:  "Authorization Basics"
date:   2023-08-22 12:35:25
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, oauth2
image: /assets/article_images/2023-08-22-authz-basics/basic_authz.jpg
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

In the code above, the `@GetMapping` annotation is standard for any Spring Boot application, denoting an HTTP GET endpoint. However, the `@PostMapping` is a bit special. It's decorated with the `@PreAuthorize` annotation, a component of Spring Security, which states that the following method (in this case, addPet) can only be accessed if the authenticated user has an 'admin' role.

When a user attempts to post a new pet to our service, the `@PreAuthorize` annotation intercepts the request and evaluates the user's roles. If they have the 'admin' role, the method is executed. Otherwise, an access denied response is returned.

You may wonder: where does this 'admin' role come from and how does the system recognize whether the user possesses this role or not?

The answer lies in the JSON Web Token (JWT) used by the application for authentication and authorization.

```json
{
  "exp": 1692800905,
  "iat": 1692800845,
  "iss": "http://localhost:8090/realms/master",
  "aud": [
    "master-realm",
    "account"
  ],
  "sub": "84059de0-00e4-43ab-82f5-5b7b217cf8dd",
  "typ": "Bearer",
  "azp": "postman",
  "acr": "0",
  "allowed-origins": [
    "https://oauth.pstmn.io"
  ],
  "realm_access": {
    "roles": [
      "admin"
    ]
  },
  "scope": "openid profile email",
  "sid": "7395ed9c-09d4-449b-9f95-60958afe5f91",
  "email_verified": false,
  "preferred_username": "admin"
}
```

This JWT is a compact, URL-safe means of representing claims between two parties. In the context of our application, the claims within the JWT relay information about the authenticated user to the application. Let's break down some of these claims:

* `exp`: This stands for "expiration time". It specifies the time after which the JWT will no longer be accepted. It's a mechanism to ensure that old tokens get discarded and are not misused if intercepted.
* `iat`: The "issued at" time claim identifies the time at which the JWT was issued. This can be used to determine the age of the JWT.
* `iss`: This is the "issuer" claim. It indicates the issuer of the JWT. In our token, the issuer is specified as `http://localhost:8090/realms/master`. Knowing the issuer is vital for validating the authenticity of the token, ensuring it comes from a trusted authority.
* `sub`: Short for "subject", it identifies the principal entity that is the subject of the JWT. Often, this is used to hold the user ID.
* `preferred_username`: This claim provides a human-readable string that identifies the user, which can be used to give a personalized user experience.

Amongst these claims, the `realm_access` claim is particularly interesting for our case. This claim reveals the roles associated with the user within the context of a realm in Keycloak, an open-source identity and access management solution. In our provided token, the user possesses the `admin` role within the realm, which correlates with the role our `@PreAuthorize` annotation is checking for.

Now, it's essential to draw attention to the mechanics of how this token is used. As highlighted in our previous chapter on authentication, this JWT token isn't a one-off; it accompanies every request made to the application. It's transmitted as a part of the Authorization header. This persistent presence means that the token, and by extension its claims, must be evaluated repeatedly. Every time the application receives a request, it needs to validate the token and identify the user and his permissions to ensure the right levels of access. This continuous validation plays a pivotal role in maintaining the security and integrity of the application's operations.

For those interested in a deeper dive into tokens and their functionalities, [Auth0's JWT Basics](https://developer.auth0.com/resources/labs/tools/jwt-basics#introduction) provides an extensive overview.

This example highlights a basic use of Role-Based Access Control (RBAC). In this case, roles within the user's token are evaluated by the Spring framework to determine authorization. However, as systems grow and become more complex, challenges can arise. A common issue is "role explosion", where the number of roles becomes too large to manage effectively. In some cases, there may be too many roles to fit within the token's constraints.

To address this, advanced systems often separate roles or permissions from the token. Instead of including every role directly, they use a user identifier, like the `sub` claim (user ID). When a user requests access, this identifier is checked against an external source to determine the user's roles or permissions. This approach is more flexible and scalable, making it easier to handle complex authorization setups.


# Upcoming

As we journey through this series, we'll delve deeper into more intricate mechanisms with practical exercises, especially Relationship-Based Access Control (ReBAC). This blog is largely centered on the latter, given our keen interest in harnessing the capabilities of [Google's Zanzibar](https://research.google/pubs/pub48190/), a powerful system known for its robust ReBAC features. By familiarizing ourselves with these advanced techniques, we aim to demonstrate the potential and flexibility that advanced authorization mechanisms can bring to contemporary applications.
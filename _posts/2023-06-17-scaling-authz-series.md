---
layout: post
title:  "Scaling Authz: A Journey into Authorization Architectures"
date:   2023-06-16 22:35:25
categories: authz
tags: authentication, authorization, security
image: /assets/article_images/2023-06-17-scaling-authz-series/scaling-authz.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Welcome to "Scaling Authz," a new series dedicated to exploring the complexities and challenges of implementing scalable authorization architectures, especially within the context of microservice environments. In this series, we will delve into the nuances of authorization, various approaches to solve common problems, and the techniques used by industry leaders to scale their systems.

The lessons shared here are not purely theoretical. Currently, I am engaged in a customer project where we are actively developing a scalable authorization architecture based on Ory Keto. We've moved beyond the conceptual phase and are now diving into the implementation process. I look forward to sharing the progress, challenges, and successes from this hands-on experience with all of you.

Before we delve into the specifics of our current journey, it's essential to highlight the invaluable experience our team at SWCode has garnered over the years. This isn't our first encounter with the challenges and intricacies of authorization at scale. In fact, we've been running a scalable system based on Casbin successfully for several years.

This prior experience at SWCode has offered us practical insights and deep knowledge about scalable authorization. The lessons we learned have been instrumental in shaping our approach and decisions as we build and enhance our authorization frameworks. We intend to share these insights throughout this blog series, in the hope that they might aid others navigating the same path.

If there's one lesson I've learned from my 8 years of cloud-native development, it's this: Never underestimate the power and complexity of authorization. The authentication stage of a user's journey, while crucial, is just the tip of the iceberg. Beneath the surface, a labyrinth of permissions, roles, and policies is waiting to be navigated and understood. Unfortunately, it's a journey many of us embark on too late.

# From Authentication to Authorization

In the early stages of most projects, the primary focus tends to revolve around authentication - confirming that the user is who they claim to be. Upon successfully validating their identity, the gates to the system swing wide open, granting them access to a broad set of features and data. Initially, this may seem like an adequate solution, particularly for applications with a limited scope or user base.

However, as business requirements expand and evolve, the needs of the system inevitably become more complex. What was once a modest application might now be dealing with multiple types of users, each requiring distinct levels of access and control. A system that started with a handful of services might have transformed into a microservices landscape, with each microservice managing its own set of resources. This deepening resource hierarchy and the need to precisely control access at various levels herald the onset of a new challenge: authorization.

At this point, many projects introduce roles and permissions in an attempt to meet their growing authorization needs. A 'role' is a descriptor for a user's function within a system, while 'permissions' determine what actions this role can perform. For instance, an 'admin' role might have permission to 'create', 'read', 'update', and 'delete' all types of resources, while a 'user' role might only have 'read' permission.

# When Simple Isn't Enough: The Pitfalls of Authorization Workarounds

For a time, this roles-and-permissions model may serve well. It's a flexible system that can handle a variety of scenarios. However, as the application continues to grow, the cracks start to show. A flat model of roles and permissions soon struggles to keep up with the intricacies of a deepening resource hierarchy and an expanding user base with diverse needs. What happens when a specific user needs access to a particular set of resources, but not others? Or when a user's permissions need to vary depending on the context or the specific attributes of the resources they're trying to access?

That's when we find ourselves in murky waters. The simple roles-and-permissions model, initially perceived as sufficient, starts to buckle under the weight of growing business requirements. The reality we face is that business requirements never stop growing. The resource hierarchy deepens and becomes more complex. And soon enough, we realize that our initial roles, often embedded within the token, no longer suffice. We start finding workarounds, implementing special handling for authorization, digging into the database, and examining roles. Some of us might even modify the business code to accommodate new requirements

The initial sense of relief that comes with implementing quick fixes and workarounds can quickly give way to a myriad of new problems. As we start to manipulate our authorization mechanisms to fit new, unanticipated requirements, we find ourselves in a precarious situation. Our previously clean and straightforward authorization process now starts to resemble a complex, intertwined web of conditions and exceptions, scattered throughout the codebase.

One significant downside to this approach is that it makes our system harder to understand and maintain. With special handling and exceptions woven into the business logic, the complexity of our codebase grows. This complexity can create cognitive overhead for developers, increasing the time it takes to onboard new team members and slowing down the debugging and development process.

Additionally, these workarounds can make our system more fragile. Since these patches are often rushed and not part of the initial design, they may not be covered by our existing test suite. This lack of test coverage can lead to unintended side effects and obscure bugs, which can be difficult to trace back to their source.

A perhaps more insidious problem is that with every workaround we implement, we start to erode the separation of concerns in our system. The business logic becomes entangled with authorization concerns. This entanglement can lead to a 'spaghetti code' situation, where the business logic and the authorization logic are so intertwined that changing one can inadvertently affect the other.

Finally, workarounds scattered throughout the business code can easily get lost or forgotten, leading to potential security vulnerabilities. When authorization checks are not centralized in an authorization layer, it's easy to miss some when making changes or additions to the system.

Arriving at this point in a project often feels like driving at full speed and suddenly spotting a red light in the distance. You must hit the brakes. It's a moment for pause, introspection, and reassessment. We must step back and ask ourselves: "How did we end up here?" It's a pivotal realization, a turning point where we understand that our approach needs a serious reevaluation.

Speaking from personal experience, I've encountered this scenario numerous times. Project after project, I've seen the same pattern emerge - the struggle to accommodate growing authorization needs within an unprepared system. Each time, it underscores the reality that this is a widespread and commonly underappreciated problem.

My message to those moving in this direction is clear and direct: Stop. Evaluate your current trajectory and consider its implications. If your project is becoming bogged down in a quagmire of workarounds and patchy fixes to accommodate for growing authorization needs, it's a clear warning sign that your current approach is not sustainable.

Realize that authorization is not an afterthought, or a 'nice-to-have'. It's not something that can be tacked onto a system with duct tape and good intentions. Authorization is a fundamental building block for any scalable, secure, and flexible system. It should be one of the cornerstones of your architecture, designed and planned for from the start.

Ignoring or underestimating this fact only leads to a technical debt that becomes increasingly burdensome to pay off. It's a road I've seen many teams travel down, and it always leads to the same destination: a complex, hard-to-maintain system that doesn't meet its users' needs or the security standards of today's digital landscape.

# Upcoming

In this introductory post of our series, we aim to underscore the criticality of authorization and the complexities it introduces, particularly in microservice ecosystems. As we navigate through this series, we will explore in-depth topics such as the requirements for a scalable authorization architecture, fundamental concepts of authorization, roles, claims, tokens, permission structures, and the impact of Google's Zanzibar system, among others.

Whether you're a seasoned architect seeking to solidify your knowledge or a beginner starting your journey in the world of scalable architectures, this series aims to be a comprehensive guide to mastering the art and science of authorization. So sit back, relax, and join me as we explore the intricate world of authorization in the era of scalability and microservices.
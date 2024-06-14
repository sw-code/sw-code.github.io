---
layout: post
title:  "Search with permissions"
date:   2024-07-10 10:30:00
categories: authz
tags: authentication, authorization, security, rbac, abac, acl, rebac, openfga
image: /assets/article_images/2024-01-01-authz-openfga-introduction/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---

Imagine you're a librarian within an Intelligence Agency. 
Agents come and go, needing access to secret information based on their security clearance. 
But what happens when an inquiry requires a deeper analysis and search, potentially yielding millions of data points? 
How do you ensure agents only access data they are cleared for, without manually checking each piece?

# Introduction

In traditional access control, verifying permissions for individual resources is straightforward. 
As we learned, we just have to check whether the user is allowed to perform an operation on the resource. 
However, the challenge amplifies when implementing search functionality. 
A search query must consider all relevant data while simultaneously enforcing authorization rules to ensure users only see resources they have permissions for. 
Authorization systems like OpenFGA focus on IDs and relationships, not on business-specific attributes like names or emails. 
This separation ensures that the authorization layer remains generalized and decoupled from business logic, 
which simplifies the authorization model but complicates the integration with business-specific search functionality.

To effectively manage search and filtering with authorization, we must integrate the authorization layer with our business-related database. 
This involves mapping business-specific attributes, such as names and emails, to the authorization data that systems like OpenFGA use.


# Filter, then check for Permissions

In scenarios with small datasets and no pagination requirements, one effective approach is to perform an SQL query to retrieve all results that match the user's search criteria based on business-specific attributes like names or emails. 
After obtaining the result set from the query, we then apply the authorization rules to filter out any data the user does not have access to. 
This two-step process allows for quick retrieval and display of relevant information by combining the business-specific search criteria with the necessary authorization checks.

While this method works well for small datasets, 
it becomes less efficient as the dataset grows and when pagination is needed. 
In larger datasets, the computational overhead of checking permissions for each result can significantly impact performance, 
highlighting the need for more advanced strategies to manage large-scale data efficiently.


# Authorize, then filter

One alternative approach is to first retrieve all resources of the relevant type that the user has access to. This pre-filtering step involves checking the user's permissions and obtaining a set of resource IDs that the user is allowed to access. Once we have this set of accessible IDs, we perform the SQL query, incorporating these IDs into the query to filter the results.

However, this approach hinges on the number of total objects the user has access to. If the number is too high, the authorization check may become slow, and including a large set of IDs in the SQL query may not be feasible. This scenario can lead to performance bottlenecks and inefficiencies, particularly in systems with extensive data and complex authorization requirements.

To mitigate these issues, the system must employ efficient strategies for handling large sets of data, such as batching authorization checks, using indexed queries, and optimizing the query execution plan to ensure that the combined search and authorization filtering process remains performant and scalable.


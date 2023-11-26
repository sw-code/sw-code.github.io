---
layout: post
title:  "The Unity Engine is your worst coworker - Here's strategy #2 to improve it"
date:   2023-10-25 10:30:00
categories: unity
tags: unity, csharp, workflow, testing, reliability
# image: /assets/article_images/unity/worst-colleague.png
author_name: Johannes Vollmer
author_link: /authors/johannes-vollmer
author_image: /assets/images/authors/johannes-vollmer/thumbnail.jpg
published: false
# target publish date: december
---

It's a cold monday, 4pm. It has been one of those days without any energy. You have not smiled a single time today. You unsuccessfully try to yank the leash on your mind to stop it from wandering around like a young cute dog.

[hook with jeff]

### WTjeFf?! (sing along!)

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd  [REDACTED].

Let's shift perspectives: What would you have done in Jeff's position?

![graphic to make them pause and actually think about it](TODO)

## What could have been


# Why so series
This blog post is part of a series: Making Unity a decent employee. Well, at least a little less horrible, I admit.

These posts are largely independent. However, you should go read the [introduction here] if you haven't done that yet, and then come back. Otherwise, you might be thinking "wtf, why are we doing all of this again?" when reading the wonderfully ridiculous code sections later in this post.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. I want this article to be generally applicable, so don't worry about it. It might be fun either way!

Here's where we are at:

The Four Strategies
-------------------
<!-- TODO copy from introduction -->


# II. Use code instead of assets (the controversial one)

## Why?
When first learning Unity, you will soon learn that compiling is slow, and tweaking values should better be done in the Editor. However, there are good reasons to use code instead of the Unity Editor in select situations.

Some advantages of code compared to the Editor:

1. Code is easier to reason about in source control diffs <!-- example: than manually hunting down those entity IDs --> 
2. Code is less fragile than checkboxes. <!-- example: settings mesh attributes stripped. also: UnityEvents references vs Components implementing Interfaces -->
3. Code allows comments. <!-- this one is important. why did we check that checkbox again? maybe something broke on ios? -->
4. Code can change a checkbox depending on some logic. <!-- otherwise only possible by hand -->

Of course you will have to try and experiment. Let me go into detail.

Reasons to use `GetComponent<X>` instead UI drag & drop:
-
-

Reasons not to do it:
-
-

## 1| Reasoning: Source control diffs

## 2| Confidence: Harder to break

Two points to explain. 

First:
Serialized data is harder to refactor than an interface definition. For example, using `UnityEvents` to reference an object may silently break. But calling a method on an interface will refactor easier.

Second: 
Fiddling with the Project Settings can make or break a build, especially when building as an embedded view into an existing iOS/Android app. 

## 3| Explain: Comments

God, I miss this feature so much! Unity is a complex beast, and unchecking a harmless checkbox can break all your animations without warning! I'd wish for some way to write down why this checkbox MUST REMAIN CHECKED in all circumstances. 

> Is there any app whoose graphical user interface that can do this? Hit me up in the comments if you know one!

## 4| Conditionally: Automate setting the settings

- Write a script that sets build settings depending on your mode (debug or release)
- Also allows you to lock settings across projects
  Regain control of variables that unity, or a plugin, might set without your consent! (Looking at you, preloaded assets!)

## 4| Conditionally: Generate Assets at Compile Time
- Write a script that replaces your android configuration depending on your build mode


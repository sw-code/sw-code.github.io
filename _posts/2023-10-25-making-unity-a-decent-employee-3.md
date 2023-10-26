---
layout: post
title:  "The Unity Engine is your worst coworker - Here's 4 strategies to improve it"
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
[jeff story]

### WTjeFf?!

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd dig up their dead pet cat called Pumpkins, and plant its severed head on their bed while they're sleeping. Well, at least I would.

Let's shift perspectives: What would you have done in Jeff's position?

![graphic to make them pause and actually think about it](TODO)

## What could have been


# What this blog post series is about
This blog post is part of a series: Making Unity a decent employee. Well, at least a little less horrible, I admit.

These posts are largely independent. However, you should go read the [introduction here] if you haven't done that yet, and then come back. Otherwise, you might be thinking "wtf, why are we doing all of this again?" when reading the wonderfully ridiculous code sections later in this post.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. I want this article to be generally applicable, so don't worry about it. It might be fun either way!

Here's where we are at:

The Four Strategies
-------------------

0. Introduction

1. [YOU ARE HERE] Write better code (duh!)
    - Raise the level of abstraction
    - Utilize basic C# language features because Unity doesn't
    - Add obvious missing C# language features using black magic
    - Create your high level Domain, instead of fiddling with unnecessary details all the time

2. Use code instead of assets (the controversial one)
    - generate at compile time
    - avoid using the editor to hook up objects

3. Smoothen the overall workflow (the one you might have expected)
    - Automate import processes
    - Automate setting the settings
    - Automate clicking the clickies
    - Seriously, let me press ONE button and then give me twenty minutes to pet my cat ... UHHHH work on something else.

4. Perform rigorous checks at build time (the banger! also black magic)
    - Check asset data
    - Check settings
    - Check code

Within each topic, I will first describe the problematic situation you too might have encountered in Unity. Afterwards I'll present my attempt at making it less horrible. I'll also share my experiences that I made after working with those solutions for quite some time. There will be code! Maybe a lot of it! Some will be controversial! 


# III. Smoothen the overall workflow (the one you might have expected)

## Automate setting the settings

## Automate clicking the clickies

## Seriously, let me press ONE button and then give me twenty minutes to pet my cat ... UHHHH work on something else.








Problem: Null Reference Exception
Problem: 


Problem: 
Solution: Logging. Disclaimer: Use AR Foundation Remote or Unity Remote, and the Editor as much as possible!! Debug your running game in scene view! Use a debugger!




Problem: Script/Object/Prefab Missing
I just wish I had a robot that would take care of such repetitive tedium for me[.](https://eev.ee/blog/2016/12/01/lets-stop-copying-c/)





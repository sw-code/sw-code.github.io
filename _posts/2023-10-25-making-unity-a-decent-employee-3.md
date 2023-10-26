---
layout: post
title:  "The Unity Engine is your worst coworker - Here's strategy #3 to improve it"
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

Think about it for a second: If your human colleague was doing what Unity does, you'd [REDACTED].

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


# III. Smoothen the overall workflow (the one you might have expected)

## Automate clicking the clickies

- Hook test execution into the build process.
  This allows you to simply add a simple Editor Tests to add another build-check.

## Seriously, let me press ONE button and then give me twenty minutes to pet my cat ... UHHHH work on something else.

- Writing a build script that runs all of your tasks

## Postconditions and Caches

Another chance to fix Unity. Sometimes, after running Play Tests, Unity doesn't care to cleanup the temporary scene file it writes to your HDD, each time you run the tests. This script automatically deletes those.










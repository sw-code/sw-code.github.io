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

[hook with jeff, that stupid asshole]

### WTjeFf?!

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd dig up their dead pet cat called Pumpkins, and plant its severed head on their bed while they're sleeping. Well, at least I would.

![image of pumpkins the cat. if you are reading this instead of seeing the image: he was cute, trust me.]()
<!-- maybe leave this image without a link intentionally? -->

Let's shift perspectives: What would you have done in Jeff's position?

![graphic to make them pause and actually think about it](TODO)

## What could have been

Just looking at the code and assets, it's hard to tell whether the game will even run. Every tiny little change has to be tested manually. A missing prefab will only result in an Error when encountered in the running Game, because Unity assumes you did that on Purpose??

If you have had the chance in your life to program in more statically tight languages, like Elm or Rust, you will know that programming can be very different (If not, please do try those)! The Elm language, for example, prevents **all runtime errors**. Yes, you read that right. You will not experience a single runtime exception in Elm. How is that possible?

If an operation can fail in Elm, BEFORE anything happens, the compiler gets up from his desk and walks over to yours:
"Hey, if they don't have the pumpkin spiced latte grande with extra glitter, should I get you something else or just nothing?"
His soothing voice is a grace. You're happy they asked you, instead of just returning with bare hands. You rest assured they'd let you know of a fire when it starts, not when your house burned down already.

Sorry, I get emotional about this topic sometimes. The point is: The Elm compiler is your friend, your pair programmer. If he does not see any problem with your code, you can be pretty sure it works. Sprinkle a few unit tests on top, and you're safe. Yes, it's a bit tedious sometimes, but for me, it's definitely worth it.

Unity however is quiet the opposite. In fact, Unity is more like the currently most popular language (he who must not be named).
If anything goes wrong, they will try to pretend that nothing happened. Happily driving a car that is slowly falling as they are entering the highway. <!-- THIS METAPHOR COULD BE MORE EXTREME OR MORE FUNNY -->

<!-- meme idea: this is fine -->

BUT don't give up! You and me, we are clever developers, we will not restrain from employing dark magic to squeeze Unity into something we can work with.

> If your strategy was instead to switch to Unreal (what are you doing here??!), please let me know how it went in the comments. I might envy you :>

# Are you series
<!-- TODO: this section is as focused as the drawer in your kitchen that contains both the scissors and the banana shaped tupperware. delete everything except the table of contents? -->

Now, in my handful of years of fulltime Unity development, I have learned one or two things, that I'm happy to share today. Maybe this stuff is widely known, maybe it's a secret, maybe I'm an Idiot lol. All I know is that I'm writing the blog posts I wish I had read when starting to developing real projects in Unity.

> I can't be the only one thinking that Unity is horribly unreliable. I often wondered how all the professional studios deal with that, and my colleagues did too. In case you know, hit me up in the comment section! 

Why care about developer confidence? Sure, you're not crashing and airplance on a mountain if you introduce a bug. But sooner or later you might want to ship something. And at that point, you want to know if everything is okay, not one month later, when the shitstorm hits your face and negative reviews rain on your dream product. However, I'm not here to convince you that you should add more tedious Tests. I'm here to show you what else is possible, so that, if you want to, you too might gain the magic powers to shape your Unity workflow.

How?

The key strategy is to find problems sooner in the development workflow, instead of at the last possible moment. Why sooner? Well, if Jeff had been telling you about the corrupted email attachment last week, you could have sent him a dropbox link the same day. This reduces the time needed to code and manually test, it increases your confidence, and therefore speeds up the whole development process.

Learning from statically typed languages, we will add checks that run while Unity is building your application. We will automate error prone manual processes. We will craft our own C#, with blackmagic and hooks! We will exploit Unity's feature of adding scripts to the Editor to make up for missing functionality, which is actually a very neat feature of Unity c:

While I'm at it, let me dial back a bit on my rant. Don't let me shit on Unity for something we can easily add ouselves. I just wish it was there by default.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. I want this article to be generally applicable, so don't worry about it. It might be fun either way!

Oh MY GOD JOHANNES STOP talking already! Let's get GOING!

The Four Strategies
-------------------

0. [YOU ARE HERE] Introduction

1. Write better code (duh!)
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

These are rather large topics and I want to go into detail about them, so this will probably be more than one article. Goo look for a link at the end of this blogpost in case I forgot to go back here and edit this text before I click the juicy UPLOAD BUTTON! :D <!-- Note to future self: I definitely don't ever want to remove this, I think it's cute -->

Within each topic, I will first describe the problematic situation you too might have encountered in Unity. Afterwards I'll present my attempt at making it less horrible. I'll also share my experiences that I made after working with those solutions for quite some time. There will be code! Maybe a lot of it! Some will be controversial! 

If there is one thing I hate more than badly designed software, it's blog posts that promise a wonderful world, but then never get to the point, leaving you hyped up. That's why I personally made sure, for you specifically, that this blog posts has some real code in it, and you're not left with a hollow promise yet again: The first blog post of this series is already online!

[Show me the code already!!1 Jesus...](TODO)


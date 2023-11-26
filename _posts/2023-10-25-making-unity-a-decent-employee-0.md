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

It's a cloudy monday morning. Your jeans are soaked from the rain on the way to the office. There's only a sip of cold coffee left in your mug as you are leaving your desk with your presentation notes.

Your heart is pounding, as you open the door to the meeting room, knowing that a dozen of people sit there, waiting.
Jeff, the coworker you teamed up with, nods and smiles, reassuring that everything is prepared. Everyone's staring at you.

"So, this is the fantastic game Jeff and I worked on", you say nervously.
"Jeff, could you please put it on the Screen?"

Jeff goes "Alright!" and plugs his Laptop in. Everyone sees the cat trying to eat cotton candy, his desktop background. 
"Jeff could you please open the Game?" What is he waiting for?

"No, I cannot open the Game", Jeff explains calmly, "the attachment in your mail last week was corrupted, so of course I don't have it. I didn't want to get in your way."

### WTjeFf?!

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd dig up their dead pet cat called Pumpkins, and leave the stiff body on their bed while they're sleeping. Well, at least I would.

![image of pumpkins the cat. if you are reading this instead of seeing the image: he was cute, trust me. not in this image though. this is a picture of the exhumined cat.](<!-- leave this image without a link intentionally, might be funny -->)

Let's shift perspectives: What would you have done in Jeff's position? What would be the right thing to do? What would be the nicest thing you could imagine?

![graphic to make them pause and actually think about it](TODO)

## What could have been

Just looking at the code and assets, it's hard to tell whether the game will even run. Every tiny little change has to be tested manually. A missing prefab will only result in an Error when encountered in the running Game, because Unity ... assumes you did that on purpose????

If you have had the chance in your life to program in more statically tight languages, like Elm or Rust, you will know that programming can be very different. (If not, please do try those!) The Elm language, for example, prevents **all runtime errors**. Yes, you read that right. You will not experience a single runtime exception in Elm. How is that possible?

If an operation can fail in Elm, BEFORE anything happens, the compiler gets up from his desk and walks over to yours:
"Hey, if they don't have the pumpkin spiced latte grande with extra glitter, should I get you something else or just nothing?"
His soothing voice is a grace. You're happy they asked you, instead of just returning with bare hands. You rest assured they'd let you know of a fire when it starts, not when your house burned down already.

Sorry, I get emotional about this topic sometimes. The point is: The Elm compiler is your friend, your pair programmer. If he does not see any problem with your code, you can be pretty sure it works. Sprinkle a few unit tests on top, and you're safe. Yes, it's a bit tedious sometimes, but for me, it's definitely worth it.

Unity however is quiet the opposite, just like most popular language in 2023 (he who must not be named).
If anything goes wrong, they will try to pretend that nothing happened. Happily driving a car that is slowly falling 
as they are entering the highway. <!-- THIS METAPHOR COULD BE MORE EXTREME OR MORE FUNNY -->

<!-- meme idea: this is fine -->

This is unacceptable behaviour in any professional environment with money at stakes. Well, at least for humans, but not for software apparently.

BUT don't give up! Let me tell you: You and me, we are clever developers, we will not restrain from employing dark magic to squeeze Unity into something we can work with.

> If your strategy was instead to switch to Unreal (what are you doing here??!), please let me know how it went in the comments. I might envy you :>

# Why so series
<!-- TODO: this section is as focused as the drawer in your kitchen that contains both the scissors and the banana shaped tupperware. delete everything except the table of contents? -->

Hi! I'm Johannes. In my handful of years of fulltime Unity development at [SWCode](https://swcode.io), I have discovered one or two goodies, that I'm happy to share with you now. Maybe this stuff is widely known, maybe it's a secret, maybe I'm an Idiot lol. All I know is that I'm writing the blog posts I wish I had read when starting to developing real projects in Unity.

> I can't be the only one thinking that Unity is horribly unreliable. I often wondered how all the professional studios deal with that, and my colleagues did too. In case you know, hit me up in the comment section! 

Why care about developer confidence? Sure, you're not crashing and airplance on a mountain if you introduce a bug. But sooner or later you might want to ship something. And at that point, you want to know if everything is okay, not one month later, when the shitstorm hits your face and negative reviews rain on your dream product. However, I'm not here to convince you that you should add more tedious Tests. I'm here to show you what else is possible, so that, if you want to, you too might gain the magic powers to shape your Unity workflow.

How?

The key strategy is to find problems sooner in the development workflow, instead of at the last possible moment. Why sooner? Well, if Jeff had been telling you about the corrupted email attachment last week, you could have sent him a dropbox link the same day. This reduces the time needed to code and manually test, it increases your confidence, and therefore speeds up the whole development process.

Learning from statically typed languages, we will add checks that run while Unity is building your application. We will automate error prone manual processes. We will craft our own C#, with blackmagic and hooks! We will exploit Unity's feature of adding scripts to the Editor to make up for missing functionality, which is actually a very neat feature of Unity c:

![a fantastic meme with bender making his own stuff](assets/image/make-our-own.jpg)

While I'm at it, let me dial back a bit on my rant. Don't let me shit on Unity for something we can easily add ouselves. Unity is an amazing piece of 21st century technology right at our hands! I just sometimes wish some minor features were there by default, so we wouldn't have to add them by hand.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. Also, I didn't touch a lot of the stuff that you might be using regularly! But don't worry, I won't jugde you for reading these articles only because of the Jeff stories.

Oh my GOD JOHANNES STOP talking already! Let's get GOING!

The Four Strategies
-------------------

0. [YOU ARE HERE] Introduction

1. Write better code (duh!)

   In addition to utilizing the boring old clean code strategies, we will add missing basic features to both Unity and C#. Everything else builds on those.
   
2. Use code instead of assets (the controversial one)
   
   We will use code to control Unity, generating and modifying assets, instead of manually editing them by hand.

3. Smoothen the overall workflow (the one you might have expected)

   Also we will automate clicking the clickies.
   Seriously, let me press ONE button and then give me twenty minutes to pet my cat ... UHHHH work on something else.

4. Perform rigorous checks at build time (the banger! also black magic)
   
   This one is huge. We will make sure that you notice bad project files before waiting for the 20 minute long build process to complete.
   I've not seen this one anywhere else yet, but I'm sure any serious developer MUST be doing that... right?

These are rather large topics and I want to go into detail about them, so this will probably be more than one article. Goo look for a link at the end of this blogpost in case I forgot to go back here and edit this text before I click the juicy UPLOAD BUTTON! :D <!-- Note to future self: definitely don't ever remove this, I think it's cute -->

Within each topic, I will first describe the problematic situation that you too might have encountered in Unity. Afterwards I'll present my attempt at making it less horrible. I'll also try to share my experiences that I made after working with those solutions for quite some time. There will be code! Maybe a lot of it! Some will be controversial!

## Find the Jeff
<!-- todo: maybe move this hint from the introduction to a surprise at the end? -->
> In each post, I'll challenge you to find the hidden Jeff.
To find him, you'll have to find the matching code section for that posts Jeff story. If you find him, just be proud of yourself. Take the first character of each heading where Jeff's code section appears. These four letters are the secret code.
<!-- TODO what to do with the secret code? GIMME SOMETHING -->

This introduction does not contain a Jeff.
<!-- because he hid in this markdown comment! congrats! :D there, get him!!
<-- --<< JEFF (hiding) >>-- -->

(Sorry if your name is Jeff, I had to choose a name. Visit me in Soest and the beer is on me!)

## A note on performance
<a name="performance"><!-- link to this anchor using `post-url/#performance` --></a>

We're still not starting with the real content. Here's why:

A recurring theme in this series will be performance. So let me get this straight, once, and link to this section a thousand times from those indivudal blog posts:

- of course performance matters
- but only in a fraction of the code
- and developer time is more important
- so use abstractions first, and optimize only where you've __measured__ a performance bottleneck

We will use crazy Reflection and LINQ and write horribly inefficient code. That is, if you were to run it every frame unconditionally. But it's only used in tests, or once at initialization, or on build time. So get ready to unlearn those annoying for-loops and write better code in less time!

All the time you save by choosing high-level abstractions, you gain for later optimization. It pays off. If this ever didn't work out for you, meet me outside, in the comment section.

## The code is now.
If there is one thing I hate more than badly designed software, it's blog posts that promise a wonderful world, but then never get to the point, leaving you all hyped up. That's why I personally made sure, for you specifically, that this blog posts has some real code in it, and you're not left with a hollow promise yet again: The first blog post of this series is already online!

[Show me the code already!!1 Jesus...](TODO)

Here you go, have fun! :)

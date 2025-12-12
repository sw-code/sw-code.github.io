---
layout: post
title:  "Gradual Blur in pure CSS"
date:   2025-12-12 22:35:25
categories: css
tags: css, javascript, filter, blur
image: /assets/article_images/2023-06-17-scaling-authz-series/scaling-authz.png
author_name: Johannes Vollmer
author_link: /authors/johannes-vollmer
author_image: /assets/images/authors/johannes-vollmer/thumbnail.jpg
---

I want to share this neat trick we discovered at SWCode. All we wanted, was a subtle background blur. 
But the road to get there was not as trivial as you might think!

# What's the problem?

This header bar uses background blur. To make it more subtle, we wanted to soften the edge. 
However, just fading the opacity of a blur is a common mistake, which looks muddy!

Let me show you an example:

![comparison of gradual blur and faded blur](/assets/article_images/2025-12-12-gradual-blur-css/map.jpg)

In this picture, two blur methods can be seen side by side: A faded blur at the top, and a gradual blur at the bottom.
Pay attention to the straight long lines radiating from the north pole. 
Observe how the lower blur makes the line look softer and softer, visuall increasing the thickness gradually.
Compare how the top blur makes the line look as if it had a ghost. The line is either thick or sharp, there is no in-between.

This makes the image look muddy, and therefore we want to avoid it.
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

I want to share this neat trick we discovered at SWCode. All we wanted was a subtle background blur. 
But the road to get there was not as trivial as you might think!

# What's the problem?

This header bar uses background blur. To make it more subtle, we wanted to soften the edge. 
However, just fading the opacity of a blur is a common mistake, which looks muddy!

Let me show you an example:

![comparison of gradual blur and faded blur](/assets/article_images/2025-12-12-gradual-blur-css/map.jpg)

Open this image in a new tab to zoom in. 

In this picture, two blur methods can be seen side by side: A faded blur at the top, and a gradual blur at the bottom.
Pay attention to the straight long lines radiating from the north pole. 
Observe how the lower blur makes the line look softer and softer, visually increasing the thickness gradually.
Compare how the top blur makes the line look as if it had a ghost. The line is either thick or sharp, there is no in-between.

This makes the image look muddy, and therefore we want to avoid it. 

For this effect to look good, it is required to blur each pixel with a slightly different radius.
But CSS does not support such a blur! So how can we construct this ourselves?


The idea is pretty simple conceptually: We blur the image with multiple different radii, and then mask the layers. 
This allows us to pick one of the radii on a per-pixel granularity. 
We can also blend those layers a tiny bit to reduce rendering artifacts.




<p class="codepen" data-height="300" data-default-tab="js,result" data-slug-hash="vEGPEvo" data-pen-title="css blur radius based on arbitrary image" data-preview="true" data-user="johannesvollmer" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/johannesvollmer/pen/vEGPEvo">
  css blur radius based on arbitrary image</a> by Johannes Vollmer (<a href="https://codepen.io/johannesvollmer">@johannesvollmer</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>
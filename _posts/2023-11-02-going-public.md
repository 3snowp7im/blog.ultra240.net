---
layout: post
title:  "Going public"
date:   2023-11-02 02:41:00 -0500
---

[ULTRA240's source code](https://github.com/3snowp7im/ultra240) is officially
public! It doesn't include any example code or resources, so it currently serves
as an educational tool if you're interested in seeing what drives the demo
videos I've posted in past updates.

The project hasn't been updated much in the past two years, but I've recently
had the chance to fix a couple bugs that I wanted to address before going
public. Nevertheless, here is a video showing a couple new features:

* Support for foreground map tiles rendered over the entity layer
* Map tile animations rendered entirely in the GPU

<p><iframe width="560" height="315" src="https://www.youtube.com/embed/7x5fYmSHQqM?si=Kgq55uXIMLFE0x0W" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></p>

GPU map tile animations are an exciting development delivering a richness to
map design without requiring any specialized code in the game loop.

The next feature I'll be working on is dynamic boundaries which will allow
the addition of moving platforms and other terrain. In the meantime, have a
look through the existing source code in Github.

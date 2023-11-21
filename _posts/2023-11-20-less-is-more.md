---
layout: post
title:  "Less is more"
date:   2023-11-20 12:00:00 -0500
categories: development update
---

At the end of my last post, I speculated on what to work on next and suggested
that I would expand ULTRA240's features. Originally, I planned on providing APIs
for additional drawing operations so that games using ULTRA240 as a framework
could render graphical elements like in-game effects, user interfaces, and HUDs.
Shortly into my planning, I decided to go the opposite route.

Rather than continue on the path of designing a game engine or framework, I want
to focus on a few well-defined operations and let the game developer handle the
rest. I started removing components and what remained are the following core
aspects:

 * Loading compiled resources
 * Efficient tile rendering pipeline
 * Animation system
 * Entity collision detection

These components will be easier to integrate with other libraries than it will
be to integrate other libraries with ULTRA240 as a framework.

And to prove it, I integrated the prolific cross platform graphics library
[BGFX](https://github.com/bkaradzic/bgfx) into my sandbox level and used it
to implement debug text and a handy collision box renderer.

![BGFX integrated with ULTRA240](/static/bgfx.png)

You may also notice that the tileset is a bit different in this screenshot from
that of older videos. I was previously using the
["2D Metroidvania Bio Sci-Fi"][1] tileset by [mtk](https://mtkpixel.art/) as art
in my sandbox level. Now the time had come to release this example application,
and I tried my hand at drawing my own, extremely limited, public domain tileset
that I could publish along with the example game's code and its other resources.
You can browse this code at the [ULTRA240 Example][2] repository. If you're 
running a POSIX compatible system, it should be trivial to compile and the run
the "game" yourself locally.

[1]: https://mtk.itch.io/2d-metroidvania-bio-sci-fi-tileset-16x16
[2]: https://github.com/3snowp7im/ultra240-example

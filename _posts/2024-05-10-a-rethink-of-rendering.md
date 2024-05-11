---
layout: post
title:  "A rethink of rendering"
date:   2024-05-10 10:23:00 -0500
---

This post is going to be mostly about low level graphics programming, and 
especially how things are done in [OpenGL](https://opengl.org).

I recently started thinking about how a user of ULTRA240 might extend its
rendering. Let's say in my game, I wanted to add an overlay effect to entity
sprites when they power up. Something like this:

<p style="text-align:center">
    <img src="/static/grim.gif">
    <figcaption style="text-align:center">
        Image credit:
        <a href="https://rgsdev.itch.io">Raphael Gonçalves</a> and
        <a href="https://www.casualgaragecoder.com">CasualGarageCoder</a>.
    </figcaption>
</p>

This could be accomplished in at least three ways:

1. *Create transparent images that overlay the player sprite.*

   This is not ideal. It's not only expensive to create these tailored assets
   for each entity tileset, but it's expensive in terms of memory.
   
2. *Apply the effect in the CPU.*

    This is certainly a more efficient use of your time, as your CPU routine can
    be used for every entity. However, it's still expensive on system resources,
    as the new tiles either occupy more GPU memory or steal bandwidth by
    requiring texture updates.

3. *Apply the effect in the fragment shader.*

    The ideal solution as it's cheap in all dimensions. The shader code is
    smaller than lots of new texture data, only needs to be uploaded once, and
    it can be reused for every sprite.

But ULTRA240 already had a shader program. Actually, it had two; one dedicated
to rendering sprites and one dedicated to rendering maps.

The sprite program takes as input the positions of all sprites and their tileset
data. The vertex shader passes through inputs to the geometry shader, which
emits the four vertices of the sprite's quad and generates the texture
coordinates for its tileset.

The map program takes as input the tile values and the map dimensions. The
vertex shader calculates each tile's position based on its vertex ID and the
map geometry. And as before, the geometry shader emits the quad vertices and
texture coordinates.

So where can a user extend these programs? Allowing the user to provide their
own fragment shader is useless unless they're allowed to also control the
attributes going through the vertex shader. Otherwise, there's no way to inform
the fragment shader what special effects are to be rendered for each vertex.
It is technically possible to allow the user to *extend* ULTRA240's shader
programs with their own code, but that is problematic. For one, such a task is
not trivial. Multiple shaders of the same type cannot be linked together, so
extending must occur by concateting shader source code. There's no standard for
how two shader source files interface, so that API must be defined, which would
also limit what the user is capable of doing and how they do it.

Another difficulty in extending shader programs is maintaining portability.
Currently, ULTRA240 supports only OpenGL as a backend, however, it is possible
to support other hardware graphics libraries. Let's say that's the case. Does
ULTRA240 expect the user to write shader code for each supported platform?
Ideally, the user employs a cross platform library that abstracts the
differences between these platforms so they can write once and run anywhere.
That's exactly what [the test level][1] does with [bgfx][2].

Rather than explore this option, I believe the answer is for ULTRA240 to *not*
have shader programs and let the user do it. The ULTRA240 "renderer" now simply
manages a texture containing the current tilesets and exposes methods allowing
the user to get all the information needed to render a frame. This includes
transform matrices for vertex positions, texture coordinates, view translations,
and projections.

This grants the user complete control over the rendering pipeline. They can add
as many vertex attributes and special effects in the fragment shader as they
want. They can even transform vertices in the CPU, or in the GPU with
instancing. But they probably shouldn't do it in a geometry shader, because, as
it turns out, [they kind of suck][3], power users [discourage their use][4],
and some platforms [don't even support them][5].
 
Those are my thoughts for now. Following the trend introduced by
[my last post](/2023/11/20/less-is-more.html), I continue to improve ULTRA240's
practicality by reducing its responsibility.

[1]: https://github.com/3snowp7im/ultra240-example
[2]: https://github.com/bkaradzic/bgfx
[3]: http://www.joshbarczak.com/blog/?p=667
[4]: https://www.reddit.com/r/vulkan/comments/91q0qx/comment/e2zytwt
[5]: https://developer.apple.com/forums/thread/88325

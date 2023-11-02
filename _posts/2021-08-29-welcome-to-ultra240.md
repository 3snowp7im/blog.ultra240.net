---
layout: post
title:  "Welcome to ULTRA240"
date:   2021-08-30 01:21:00 -0500
---

Welcome to the ULTRA240 development blog. ULTRA240 is a game engine designed to
power retro-style platformers. I'm Mouse, author of [sotn.io](https://sotn.io),
the Symphony of the Night randomizer. If you know me at all through the SotN
community, you know I love the Metroidvania genre in general, and the
synergistic movement options in SotN specifically. If you're not familiar with
the concept of synergistic movement, my fellow speedrunner PCTrax has a
[great video on the topic](https://www.youtube.com/watch?v=XsSqZLDXmTU) over on
his YouTube channel.

While Metroidvanias are a popular genre for indie games of recent years, the
recent titles mostly focus on exploration, and only employ movement options to
further that cause. As a result, movement options tend to exist only to open
previously inaccessible paths, rarely rewarding the player for employing them
in their regular pathing. On the other hand, SotN's unique implementation of 
transformations allow the player to blend all its movement tech to the point 
that, when mastered, going from points A to B becomes significantly faster and
just feels fun in and of itself. That is my inspiration for the game I'm
working on in my spare time. To facilitate that goal, I decided to write a game
engine specifically designed for open world platformers.

The project is still in the very early phase of development, but I have a good
idea of where I'm going with it. As the name implies, ULTRA240 renders in 240p
(256 x 240). I'm currently targeting the POSIX platform, but all
system-specific code is abstracted into modules that can be ported easily. To
that end, the media layer is implemented on top of the cross platform 
[SDL](https://www.libsdl.org/) library.

The engine will abstract away all minutiae of creating a window,
loading resources, drawing graphics, playing sounds, detecting player input,
and handling entity collisions. Everything else must be handled by the game's
runtime, including handling player input, implementing movement, entity loading
strategies, and even the game loop itself. A companion SDK exists to compile
[Tiled](https://www.mapeditor.org/) resources into a custom binary format, and
to convert PNG tilesets into uncompressed image files. 

I've been keeping a video record of new features as I've been adding them. The
first video I made demonstrates creating a window, loading a map, and scrolling
the screen around to show off a parallax effect in the background elements.

<iframe width="560" height="315" src="https://www.youtube.com/embed/yrK8Lh0HS_4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

A few weeks later, I added a mechanism for loading and drawing entities.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Iw8WIaDAIeo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The next step was adding an easy interface for controlling animations created
in Tiled's tileset editor. That led to giving the player control over their
entity and implementing some very rudimentary collision detection.

<iframe width="560" height="315" src="https://www.youtube.com/embed/C9g22whP4YM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I then improved the collision detection and added the ability to transition
between maps.

<iframe width="560" height="315" src="https://www.youtube.com/embed/URoBS2PQ7iY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Recently, I've revisited the animation system to improve it's handling of
collision boxes that change between frames. This is necessary in order to
ensure the player can't push themselves out of bounds by moving their entity in
a way that changes their collision box near a map boundary. I think at this
point it's been mostly worked out, however, I still need to test it by adding
more movement options, which serendipitously brings me back to reason I started
working on this project. I may have some more to say on that in a later
entry. I also believe that the way map boundaries are handled is worth a deep
dive into at some point as well.

Finally, to lightly touch on the topic of the blog itself. It exists mainly as
a way to consolidate my documentation of this project. That documentation might
include articles on how some features were implemented as well as walkthroughs
of the code. It is my hope that it might help some others, or, at the very
least, might serve future me as a fun retrospective on this project's
development.

Thanks for reading.

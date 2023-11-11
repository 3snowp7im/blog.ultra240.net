---
layout: post
title:  "Respecting boundaries"
date:   2021-11-22 14:41:00 -0500
categories: boundaries maps collision animation
---

In my previous post, an introduction to ULTRA240 and to the blog, I briefly
outlined the goals (and non-goals) of the project and shared some general
development history. I avoided getting into technical details where possible.
Today's post will be quite the opposite; I'll drill down into a specific aspect
of the engine and plan on getting a bit more technical. As you might have
guessed from the title, the topic at hand is staying within boundaries. I have
very little experience developing platformers, so consider this a fair warning
that the following may not be optimal for the particular dimensions you're most
concerned with as you implement your own solution. What I'm about to share is
simply *my* best attempt (up to the time of my writing this) at implementing a
solution optimal for the dimensions *I* am concerned with. If at any point I
revise this solution, I'll link the update here.

Back to the topic and the problem at hand. First, let me state the problem
succinctly: How do you keep things in bounds? If you're not familiar with this
gamer lingo, "bounds" refers to the boundaries or borders of an area. It's
likely that you're currently "in bounds" of a room as you read this article. The
walls, ceiling, and floor of your office or living room, for instance. These
boundaries separate you from the other rooms of the building, and ultimately,
from the outside. The nature of electrostatic forces prevents the particles that
you consist of from slipping in-between the particles that make up the floor, 
despite gravity's constant effort to pull you together. Conveniently, the
physics in our observable universe have a way of keeping solids such as yourself
within the bounds of other solids.

Not all boundaries must be physical. Traditional sports define an area of play
that is seldom enforced with barriers, however, any action "out of bounds"
usually results in a penalty of some kind. Field dimensions are standardized
around what players can realistically be expected to traverse during the course
of a match. The same can be said of many video games. They too define an area
of play and expect the player to stay within its confines, and, like in many
physical sports we can't rely on natural properties of matter to keep things in
bounds.

That is not to say that performing actions "out of bounds" must be an
impossilibity. To the contrary, it is possible to get out of bounds in *many*
games, whether or not that's a feature that the games' developers intended the
player to exploit. Usually, it is not and developers try their best to keep
players in bounds. Most games have intended routes or at least finite design
elements. An out of bounds player may have the opportunity to bypass the
developers' intended objectives (often called performing a sequence
break) or even simply result in a less-than-polished user experince. It is
therefore the developer's responsibility to make it *very difficult* to get
out of bounds, and some manage to do it better than others.

Generally, we want to simulate *collisions* between the player and the
boundaries. We *detect* when the player is going to cross a boundary, and
prevent them from crossing it. This is a form of *collision detection*, 
altought that term is often used broadly to describe interactions between the
player and any other type of entity, not just boundaries. When games fail to
detect a collision between the player and a boundary, it is commonly called a
*glitch*, or, more specifically, an *out of bounds glitch*, or even simply
"an out of bounds". As a side note, there are *many* categories of glitches,
many of which are exploitable by
[speedrunners](https://en.wikipedia.org/wiki/Speedrun) to beat video games fast.
It is even common for speedrunners to *combine* different categories of glitches
in order to "break" the game, as we'll soon see.

Collision detection is usually performed in one of two ways: *a posteriori* or
*a priori*. *A posteriori* is to apply a movement vector to an entity and then
determine if the entity intersects with a boundary at its new position:

<div style="text-align:center">
    <canvas id="intersection" width="480" height="240"></canvas>
</div>

This method of collision detection is very popular because it is easy to
implement and usually produces the correct result. As long as your movement
vectors are small enough *per frame*, entities are guaranteed to intersect with
boundaries at some point along their path before they have a chance to get out
of bounds. However, what if in a single frame an entity is able to acheive a
large enough movement vector to completely cross a boundary?

<div style="text-align:center">
    <canvas id="escape" width="480" height="240"></canvas>
</div>

Since the entity's position doesn't intersect with the boundary after the
movement vector was applied, this method of collision detection is happy to
allow the entity to pass through the wall and possibly get out of bounds.
This is the basis for a few sequence breaks in Super Mario 64 involving the
famed *Backwards Long Jump* speed-building technique. Since Mario has *no
maximum speed cap* when moving backwards *and* the game implements collision
detection using the *a posteriori* method, the player is able to stack glitches,
building up enough speed to completely pass through boundaries in a single
frame.

Alternatively, the *a priori* method of collision detection calculates the
moment of intersection regardless of the movement vector's magnitude. It can be
thought of as determining an intersection between a boundary and the movement
vector itself. It is a bit more expensive and difficult to implement, but it
prevents sequence breaks that exploit speed in order to get through walls.
I chose to implement collision detection using the *a priori* method for
ULTRA240 and, in a future blog post, I might go into some more detail as to
how that's done.

That about wraps it up on keeping the player in bounds when changing position.
Let's now briefly cover the issue of changing *shape*. A popular technique used
to add more depth to gameplay is to give the player a moveset that changes the
shape of their *collision box*. A collision box is, just as it sounds, a
rectangular area specifiying the space where an entity collides with other
things. They are, in essence, performing the same function as the game's
boundaries, but rather than defining the area of play, they define the size
and shape of the things in play. A collision box can be the same dimensions as
its sprite, but usually, developers make the collision box smaller in an effort
to be a little "forgiving" to the player.

Crouching, jumping, sliding, and dashing are all examples of actions that can
shrink or otherwise change an entity's collision box. When transitioning from
one collision box to another, special care must be taken to ensure that the new
collision box stays in bounds. Take for example, a slide animation that shrinks
the player's collision box while giving them some forward movement before
eventually allowing them to rise back to a standing position and return their
collision box back to its previous height. If the player is able to take
advantage of this shorter collision box by sliding into a tighter space than
they would normally be able to walk into, then returning their collision box to
its full height effectively allows the player to partially clip out of bounds.
Depending on how collision is detected, this might be enough to allow them to
get completely out of bounds very easily. Some games that correctly detect a
partially out of bounds player attempt to adjust the player's position until
they are no longer intersecting a boundary, only to chose the wrong direction,
"zipping" the player into a different area of the game, or even completely out
of bounds.

Interestingly, Symphony of the Night manages to both get it right and wrong in
such scenarios. It is possible to divekick towards a tight space, colliding with
the ground in a crouch animation, and riding the momentum to slide in. The game
let's you become "stuck" in there, refusing to let the player go back into a
standing position and walk out. This was very deliberate, as they designed a
"wiggle" mechanic specifically for freeing the player in such a predicament.
By alternating left and right directional inputs, the player is able to wiggle
themselves free.

What the developers *did not* account for was the player *taking damage* while
being stuck. In such a case, the damage boost animation overrides the crouch
animation, expanding the player's collision box, *and* applying a movement
vector. This is more than enough to knock the player completely out of bounds.
The problem is exacerbated by the fact the player is able to use an in-game
item to damage themselves whenever they like, providing a reliable method of
getting out of bounds.

The solution here isn't as clean as choosing this algorithm over that. Rather,
it comes down to designing the interface appropriately. A collision box that
doesn't fit within the bounds at its current position must *never* become
active. To enforce this, ULTRA240 will refuse to transition into an animation
that has an associated collision box that intersects with the game's boundaries.
Indeed, an *a priori* collision detection method is built into the animation
system itself. Any game mechanic requiring a new animation will have to abide by
this design choice and be capable of handling a refusal.

In this article I've merely touched upon two of the most important aspects to
consider when trying to keep the player in bounds. I've gotten a bit technical
without going over any actual code, something I might want to do in future
installments. For now, I'll wrap this all up with a brief demo of this tech at
work. The most notable addition from the previous videos are foreground layers,
and the implementation of jump and fall animations with dynamic collision boxes
that can safely change during different animation frames.

<iframe width="560" height="315" src="https://www.youtube.com/embed/B2Ay7ZyE-Lw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<script>
function drawImage(ctx, src, x, y, dx, dy, alpha) {
  return new Promise(function(resolve) {
    const img = new Image()
    img.onload = function() {
        const prevAlpha = ctx.globalAlpha
        ctx.globalAlpha = alpha
        ctx.drawImage(img, x, y, dx, dy)
        ctx.globalAlpha = prevAlpha
        resolve()
    }
    img.src = src
  })
}

// Intersection
{
  const canvas = document.getElementById('intersection')
  const ctx = canvas.getContext('2d')
  ctx.lineWidth = 3
  drawImage(ctx, '/static/tux.png', 180, 60, 100, 100, .5)
  drawImage(ctx, '/static/tux.png', 300, 60, 100, 100, 1.)
  ctx.strokeStyle = 'blue'
  ctx.strokeRect(120, 0, 240, 240)
  ctx.strokeStyle = 'red'
  ctx.beginPath()
  ctx.moveTo(280, 100)
  ctx.lineTo(310, 100)
  ctx.lineTo(300, 90)
  ctx.moveTo(310, 100)
  ctx.lineTo(300, 110)
  ctx.closePath()
  ctx.stroke()
}

// Escape
{
  const canvas = document.getElementById('escape')
  const ctx = canvas.getContext('2d')
  ctx.lineWidth = 3
  drawImage(ctx, '/static/tux.png', 180, 60, 100, 100, .5)
  drawImage(ctx, '/static/tux.png', 370, 60, 100, 100, 1.)
  ctx.strokeStyle = 'blue'
  ctx.strokeRect(120, 0, 240, 240)
  ctx.strokeStyle = 'red'
  ctx.beginPath()
  ctx.moveTo(280, 100)
  ctx.lineTo(380, 100)
  ctx.lineTo(370, 90)
  ctx.moveTo(380, 100)
  ctx.lineTo(370, 110)
  ctx.closePath()
  ctx.stroke()
}
</script>

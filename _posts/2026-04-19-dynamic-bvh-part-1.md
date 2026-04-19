---
layout: post
title: "dynamic BVH - part 1"
date: 2026-04-19 14:39:00 +0200
categories: [tech, math]
---

After a year without doing any research, I've been wanting to start again, maybe this time with a bit more math and less programming.

I've always found that, as a software engineer, the math tools I need to properly model and maybe provide even partial answers to my problems are just...not there.

This occurs in particular when considering almost any data structure with non-trivial topology (e.g a tree) in the context of *animation*.

We can say a lot about a lot when things are static, but when they start moving we just turn to God and pray for the best. We also plot some performance metrics from time to time to reassure ourselves.

---

That's why I've been looking into *dynamic BVH*: you want a BVH to speed up your ray tracing, but when your geometry moves you don't know whether you need to rebuild it or not.

The usual cost metric to measure the "quality" of a BVH is the *Surface Area Heuristic (SAH)*, which has a nice interpretation: it counts the number of ray-primitive intersection tests you will have to do on average, assuming that rays come from every direction with equal probability.

So here is my goal: provide some math tools to better quantify the SAH cost of a BVH and its evolution when geometry moves.

This looked super complicated, so I figured I'd just get some intuition in 1D.
Spoiler alert: it's easier, but it's not easy.

---

I'm in 1D, my primitives are just intervals. Some more assumptions: the intervals form a partition of the unit interval $$[0, 1]$$.

Turns out this is an already existing and throughly explored problem, called *alphabetic coding*.
Very similar to the classic *prefix coding* problem in information theory, but with one extra constraint: the inputs (in our case the intervals) are ordered, and the encoding must maintain that order.
In the full binary tree that represents this encoding, this corresponds to the order of the leaves when visited with a pre-order traversal.

And yes, in this precise scenario, the SAH cost and the average message length of the alphabetic coding are pretty much the same thing. Noice.

There is a variety of algorithms that allow you to have an optimal alphabetic coding, the fastest ones run in $$O(n \log(n))$$.

Because this generates a prefix code, information theory gives us $$ C \geq H $$, with C the tree cost and H the entropy of the *interval-length distribution* (remember they form a partition of the unit interval, so the lengths sum up to 1, thus forming a probability distribution).

Another algorithm (by Gilbert and Moore) runs in $$O(n)$$, is extremely simple to write, and returns a non-optimal-but-not-too-bad result, with $$C \leq H+2$$.
I love this kind of stuff. So neat.

---

With that in mind, I wanted to add my little contribution to this field: what if we could, starting from a non-optimal encoding, gradually improve it with some local tree modification?

This way, if you start from a tree (optimal or not) and then your primitives evolve over time, you could always try and improve it with some local changes, thus removing the need for a full rebuild.

I looked into 2 promising local tree modifications: one called *rotation* which apparently is kinda famous in that niche field, and one that I came up with and I called *leaf-jumping* (as the name indicates, the idea is just to take a leaf and make it jump onto a neighbor).

After digging quite a lot I unfortunately found out that this was a dead end: both these operations can get you stuck in local minimas.

Claude found counter-examples. I checked them myself. Claude was right. Ouch.

---

Now where can we go from there?

Well if these local tree operations can't always lead us to a global cost minimum, they still provide a cheap way to improve the current tree, or at least not make it worse.
Leaf-jumping in particular is *really* cheap.

In the 1D case with all the assumptions we made, this should provide a pretty efficient algorithm if our only goal is to keep our cost below $$H + 2$$:
start with Gilbert&Moore, and whenever the geometry changes try some leaf-jump operation(s) if they can decrease the cost, and if at some point we go above $$H + 2$$ then do a full rebuild with Gilbert&Moore.
I should run some experiments to see how this goes.

But there are also some other very cool stuff to investigate: in this 1D simplified scenario, could we study the cost evolution depending on the evolution of the interval-length distribution? and how can we adapt the very interesting results coming from alphabetic encoding theory to our computer graphics scenario, where the primitives definitely do not form a partition of the unit cube?

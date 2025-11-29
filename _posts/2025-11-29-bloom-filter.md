---
layout: post
title:  "bloom filter"
date:   2025-11-29 14:43:00 +0200
categories: tech
---

I had a lot of topics in mind for this 1st tech post, each one more complex (and fascinating) than the other, but in the end I chose the simplest one: Bloom filters.

Start simple and small, then build up to something more intricate. When in doubt, that's always a good way to go (in my experience at least).

Anyway, Bloom filters: what about them?

I didn't know about their existence until quite recently, and I was surprised that none of my teacher ever mentionned them while I was studying computer science.

You can see them as an optimization structure to accelerate some queries on a *set*.
In particular, if you have a set of objects and you want to know if some other object belongs to that set or not, then a Bloom filter can tell you two things:
- no, the object is definitely not in the set
- maybe, you'll have to ask the actual set interface for a more precise answer.
And the whole point is that it can give you this answer *very quickly*, and the filter's memory footprint is also super light.

How does it work?

The filter is composed of B bits and H (independent) hash functions, all of them taking an object as input and returning an integer between 0 and M-1.
Initially all M bits are set to 0.
When an object is added to the set, we compute it's H hashes, all of which providing a bit position that will be set to 1.

Now when you want to know if some object belongs to the set, compute it's H hashes and check that all corresponding bits are all 1s.
If not, then this object is definitely not in the set. Otherwise well...we don't know, so we pass the query onto the actual set interface!

Since the hash functions are independant, any object should be mapped to M "random" bit positions, therefore it's very unlikely that an object that is **not** in the set actually passes the filter test.
And that's where the true beauty of Bloom filter is: since this model is pretty simple, we can actually quantify this probability (with actual math and calculus yes!), depending on B, H, and the number of items in the set.

I'm not gonna do the maths here, mainly because I haven't figured out yet how to make math expressions work with Jekyll, but they're easy to find online.


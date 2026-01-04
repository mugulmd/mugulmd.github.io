---
layout: post
title: "the local LLM adventure"
date: 2026-01-03 18:52:00 +0200
categories: tech
---

About two weeks ago I decided to deploy a local LLM on my laptop. Why? *Because I can.*


Indeed, I do have a GeForce RTX card on my laptop: it's tight, but it should work (in fact, countless other people have done it, so I was confident it would work from the very beginning).


I wanted to use this LLM for AI-assisted coding: for a hobbyist developer like me this is gold because you get to spend a lot less time on the stupid stuff (and therefore more of that precious time is spent on the interesting bits).


I quickly discovered I was missing Nvidia drivers on my machine (not a good start). So I went ahead and installed them with some Ubuntu utility command line and........**kaboum**.

Black screen, corrupted OS files, panic attack.

I followed some online advice, met with the lovely "grub shell", gave up, ate continuously for 3 days in a row because *Christmas in France*, and finally took a good decision: use a bootable USB stick.


I'm not an expert at repairing broken computers, so I just backed up my files and completely reinstalled my OS from the USB stick.

Then I faced my nemesis again: the Nvidia drivers. I tried with some other command line, and for some reason it worked just fine. *sighs*


It was finally time to start doing some LLM-oriented stuff.

Because of my *french-ness* I figured I'd use Mistral Vibe for AI coding, and their doc advised to use **vLLM** for offline model inference.


Indeed on very small models (1.5B) it works great, and it is very very very fast.

But as model size grows to something actually useful for coding (7B), we hit a major blocker: not enough GPU memory.


So I tried **llama.cpp** which is said to be much more adapted to this kind of configuration, and indeed it could hold the 7B model, but inference was so slow I quickly gave up (5 minutes to generate a Python script that prints "Hello world"???).


And I went back to vLLM, because speed matters to me. A lot.

I discovered that some clever people thought about *quantizing* model weights to make the model lighter without compromising too much on quality, and that's how I ended up with this bad boy: `Qwen2.5-Coder-3B-Instruct-AWQ`. Fast enough and clever enough for me, 32K context length.

Mission accomplished!


One thing I haven't tried though: maybe llama.cpp works better with *Llama* models (since it's *litterally in the name*)? In that case, it would be interesting to try a *CodeLlama* model with 7B weights. Another story for another day!

---

Quick before I forget, the album of the week: *GOOFY & SEXY* by *nelick*.

I usually don't care much for French rap, but keeping an open mind leads to good surprises :)

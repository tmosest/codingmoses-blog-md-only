---
title: Day 21 HomeLab - 
description: Placeholder
date: 2025-09-15
categories: []
author: moses
tags: []
hideToc: true
---

# Upgrading Bezalel... 

Finally got all the [parts](https://www.thingiverse.com/thing:4975346) we need... God this print was a beast... 

Just need to take some things apart now...

Screws... screws... screws... and a bad quality print but we got it attached!!!

Also replaced the fan that was inside that powerbox with a larger one that cools off the entire bottom but now we need some new feet...

- [Feet with feet lol](https://www.thingiverse.com/thing:5538153)
- [Most practical](https://www.thingiverse.com/thing:6672580)
- [Hmmm springs](https://www.thingiverse.com/thing:3660131)
- [Elegantly simple](https://www.thingiverse.com/thing:5160624)
- [Ummmmmmm not sure if this will balance well](https://www.thingiverse.com/thing:3856997)
- [And ADHD take over...](https://www.thingiverse.com/thing:3542205)

- [What took you so long... well you know master...](https://youtu.be/_ADoDPp7NP0?si=YNWgrtCsirHIfN_V&t=99)

Ok, that is getting much closer to functional. I propped it up on some old printer cases for now and it homes fine but halted when trying to set the z offset. 

Need to investigate further into what I did wrong.
But I am going to time box this for now.

Celebrimbor is still working great. However this one raspberry pi case refuses to print correctly so we are gonna move on from that as well and print some feet...
Cause feet pics are where the money is at... You can find me on only fans if this doesn't work out as Big Foot.

# Red Five / PiHawk / TurboPi

Pi Hawk (Red Five) is getting closer to being done. The hardware is basically assembled now. Just figuring out the software and making it work with the controller.

- https://docs.px4.io/main/en/flight_controller/pixhawk_series
- https://www.youtube.com/watch?v=nIuoCYauW3s
- https://ardupilot.org/dev/docs/apsync-intro.html#apsync-intro

## Furby Five...

Imagine a 90's furby on top of a quadcopter yelling "weee do it again" over and over again as it flies through the sky!

Well if we can turn them into a [musical instrument](https://www.youtube.com/watch?v=GYLBjScgb7o) then we can do anything!

// TODO coming soon... 44 Furby delivery drones!

# Hot Wheels Spy Car

A long time ago I took apart a hot wheels car to make a mini RC car. 

The components: // TODO Amazon links etc.

1. Small DC motor for back wheels.
2. Small linear motor for turning the front wheels.
3. Battery.
4. ESP 32-C
5. ... Missing male to male connectors for testing the prototype... well here is more money Amazon...

# Pip Boy 

Got voice detection working which is really cool. I can basically do a captains log now.

We are going to run a small LLM on this thing and see how that goes.

`ollama run llama2:13b`

```
With 16 GB of RAM, your Raspberry Pi can comfortably handle larger language models than the standard 8 GB model. Here are some options: 
Llama2:13b: A robust model with 13 billion parameters that performs well on a 16 GB system.
Phi-3 Mini (14b): A 14 billion parameter model that is efficient for its size.
Smaller models: You can also run smaller, faster models like phi or tinyllama. 
```

Tested this out with the 7GB they both seem to work pretty well. 

Got Kali linux working on it but needed to fork and update this repo to Python3.

- https://github.com/tmosest/katoolin

It ended up being smoother and better to just have the pipboy do the voice to text and then send that to the Ollama server for processing.

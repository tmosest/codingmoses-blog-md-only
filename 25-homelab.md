---
title: Day 25 HomeLab - 
description: Placeholder
date: 2025-09-19
categories: []
author: moses
tags: []
hideToc: true
---

# Vertical Farming

“A society grows great when old men plant trees in whose shade they shall never sit.” — Greek Proverb

Got a dedicated server working for this. For now the plants will be stationary but in the future it would be nice to replicate what dyson has done.
The lights will be automated and a raspberry pi zero with a camera will use a rope system to move around and take photos.
The QR code will open the local plant wordpress site and plugin like before.
One of the bottle necks last time was name generation, planting, and initial creation so we will need to make those much more efficient.
Also QR code generation.

- [QR Code and Name generation](https://github.com/labbots/NiimPrintX/blob/main/NiimPrintX/nimmy/printer.py)

## Name generator

Going to automate this by having a CSV file of names and it will pick one at random that is not already in use.
Local Chat GPT has generated a bunch of LOTR, Disney, etc names.
That will make this a lot easier.

## API

Need to hook up the Wordpress API again. That way I can have other devices create and manage plants easier.
Pipboy will be able to listen for commands and then generate the plant name and print the QR code label for me.
Then later my unnamed robot worker that takes photos can also make calls to the API to upload his photo of the plants and update the status.
Then this could also feed into a LLM or Vision AI model that knows more about the state of the plants.

## Plant Photographer

Want a small robot that can keep look over the plants. The idea is to use paracord and it can zip line around basically.
For now we will only worry about 1D and move up to the 2D plan next. 1D should be enough to go back and worth on a single shelf.

Thingiverse inspirations. Want a trolly cart basically. It will allow somthing like a pi zero to travel around and take photos.

- [High Torque Gearbox](https://www.thingiverse.com/thing:4790867)
- [Sg90 gear micro](https://www.thingiverse.com/thing:3023305)
- [Sg90 gear](https://www.thingiverse.com/thing:4138417)
- [Car cable system](https://www.thingiverse.com/thing:1748449)
    - Looks fun for outside.

## Water Dispenser

After we have that working we will continue my automated water dispenser and fertiziler project from a while ago.

# Octoprint... Cause you know...

We now have some many feet that we have devalued the market... so luckily the new front fan mod I made in TinkerCad is ready.
And it worked just fine! Ironically, I moved it way too high but 2 long screws and 4 nuts easily fixed that problem.
I am having some issue with the extruder or nozzle though... sounds like the extruder skips because the thing is clogged or something...
Probably a direct drive would help alot... time boxed...

## Hallloween 

Thinking about fun animatronics etc to make for halloween...

# Hey Pip Boy

Back to the pip boy... now when I say "Hey pip boy I planted ..." it will grab the arguements of "..." and send them to my Wordpress API and generate a new randomly named plant!

and wooooooohoooooooo it even prints the lables with the QR code for the robot!!!!

I had to catch up on some sleep debt so I'm starting the next day now...
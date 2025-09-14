---
title: Day 18 HomeLab - 
description: Placeholder
date: 2025-09-13
categories: []
author: moses
tags: []
hideToc: true
---

# Upgrading Bezalel

Since this printer is unoperational because of bad firmware we are going to continue to upgrade the hardware.

First though we are going to confirm that a firmware update will fix things since the other printer is now working great.

That's as simple as inserting an SD Card now since we still have everthing working. I will experiment with more images and changing the printer name first again though.

Also, I need to make my configs public, maybe put in a pull request to the authentic repo as well so others have an easier time. 
I don't know if anyone still uses this model of printer to be honest. 
I can also make a bot that helps with the 3D printer community on Reddit which would be cool.

## Hardware Upgrades

Now that we have confirmed the firmware upgrade works.
The x-axis motor was also unplugged. That was a quick fix, luckily, I thought the board had been fried when auto home didn't work.
Debugged it by moving each axis independtly. 

1. Gonna make the Academy of Bezalel the image for this printer. Leaving ironman for Celebrimbor.
    a. [Instructions on how to change the image in Marlin](https://www.instructables.com/How-to-Make-a-Custom-Boot-Screen-for-Your-Marlin-3/)
    b. [Website for generating Marlin bitmaps](https://marlinfw.org/tools/u8glib/converter.html)
2. Change the name to match as well.
    - Easy

Pull out the old fans near the extrude and then... um where are my box of screws... 
Now worries, we order more screws and we also continue with the cable layout and soldering.
Plus we can just be a little creative with the old screws. American indignuity is the bread and butter of the PNW.
The American's Alchemic receipe.

Speaking of which I had to put together a bread board quickly to test my multimeter. It wasn't getting the correct voltage from the printer.
Using my old setup for testing robot servos with my bipedal, I was able to confirm that both the multimeter and the little voltage displays from Amazon are working fine. 

Was able to get the fans installed also added some LED lights that were 24V (same as ender). Only one small problem was that the 40 mm fan adapter I printed was for 3 and the belt that controls the x axis position is underneath on those while it is to the side on mine. This makes it in the way.



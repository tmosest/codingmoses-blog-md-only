---
title: Day 18 HomeLab - 
description: Placeholder
date: 2025-09-12
categories: []
author: moses
tags: []
hideToc: true
---

# Octo Print / Marlin / Hardware Upgrades

Yesterday was a reminder that 3 printers can be tempermental at best sometimes. 
I wanted to print a case for a Raspberry pi 5 specificy... even though it had no problem generating over a dozen pi 3 cases it failed at the pi 5.

I tried raising the bed temperature and slowing down the speed of the print since I had increased it to almost draft levels for the previuous one.
The bed leveler isn't working either so I need to see what is up with that plugin configuration.

My problem is that the ender 5 pro printer can have a few different boards depending on when it was made.
Therefore we must take them open again...

Which means it is a good time to also make some hardware upgrades potentially...???
or maybe we should learn a valuable lesson and make sure we always have atleast one working printer for each model instead.

Found some NF-A4x10 and 20 fans while cleaning and some buck converters as well so we are going to continue to make those upgrades as well.
 - Just need to print the enclosers for the buck converter first so they don't explode from touching metal for too long...
 - 20 MM goes inside the printer extruder to replace that fan.
 - 10 MM goes inside the case instead.

Speaking of which we have improved the safety of the operations by including a kill switch for both printers. 
Next, will be to add a temperature sensor to the pi's. Also an enclosure for the printers. Finally, a robot that can put out fires.

Figured out what was wrong with the 3D printer and got the bed mess vizualizer working again.
Had to use the fine tuner baby stepper to set the z offset. The software might not know where the BL Touch is. 
I might have put a positive when it was suppose to be negavative or vice versa.

I might have printed the new buck converter case too large. These ones have the voltage meter in them making them a bit bigger.

Setting the Z offset didn't help so I added the missing g code configuration for the vizualizer plugin and that somehow broke the whole printer.

Now I am just going to reflash the whole printer...

1. The Octopi marlin plugin doesn't want to work so now I am walking up and down the stairs which is good for me.
2. The changes in the newest versions of 2.1.x were annoying to deal with. Had to look through most of the config to compare it my old one.
3. Every solution seems to generate a new problem.
4. Feels like a lot of work for one buck converter case lol...
5. Reminder it is not just one buck converter case... it now prints much better and is safer... more working prints are great!

# Pipboy 

The host name was wrong had to edit the following 2 files:

```
/etc/hostname and /etc/hosts
```

First thing we will need for the end of the world is wikipedia! 
That way when I'm just a brain in a jar in some vault, I will have plenty of reading material on my.... wrist?..... um new plan?
Become a ghoul!

- [KIWI Offline website reader](https://en.wikipedia.org/wiki/Kiwix)

```
sudo apt update && sudo apt install kiwix kiwix-tools;
```

Then had to go download the ZIM files for Wikipedia etc.


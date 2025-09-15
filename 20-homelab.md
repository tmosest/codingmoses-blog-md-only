---
title: Day 20 HomeLab - 
description: Placeholder
date: 2025-09-14
categories: []
author: moses
tags: []
hideToc: true
---

# Upgrading Bezalel / Octoprint / 

Going to continue with where I left off yesterday. Just waiting for the print to finish.
This new one should work fine. It is somehow in a quantum build state... 
One piece is just fine and the other center piece has become nothing but a ball of strings...

Had to cancel that print because the wifi went down... so I took that time to redo the homelab networking.
The cable management was really bad...

Well I just made a terrible mistake... RIP Pi 3... I already knew this could be an issue but I have been so good with it that I never thought about it again.
I was distracted by the dog barking and put the cable in the wrong pin... This board might be trash now...

```
Yes, the Raspberry Pi's microUSB (or USB-C on Pi 4) port includes a polyfuse for over-current protection, but the GPIO pins do not. Powering the Pi through the GPIO header bypasses this protection, leaving the board vulnerable to power-related issues like short circuits or over-voltage if not handled with care. 
```

No worries, lessons learned there. Just upgraded it to use an old micro USB charging cable instead. 

Only problem now is the long wait time for the other printer to print the correct air flow nozzles for her... and well Celebrimbor was having some issues...

Plus I was playing Daddy Dog Care... lol... so it goes...

---
title: Day 14 HomeLab - Back Again, LeetCode is back don't tell a friend...
description: Solving over 100 Leetcode problems in one day without typing! Look mom no hands!
date: 2025-09-08
categories: [octoprint, appleScript]
author: moses
tags: [octoprint, leetCode, appleScript]
hideToc: true
---

# The Big Bang!

This morning when I returned from walking the dog there was a giant explosion!

```
"There's a fine line between wrong and visionary. Unfortunately, you have to be a visionary to see it". 

Sheldon Cooper - The Big Bang Theory
```

It sounded like a transformer had exploded and it was at that moment I knew who it was...

Girl Scouts!!!

- [Psych It's The God Damn Loch Ness Monster!!!](https://youtu.be/pS_zE0DTWIU?si=F7WiZU2ZosdQ4xr4)

No, the only thing worse than epsilons and deltas with boxed diabetes... JK Love you :)

THE SQUIRRELS obviously...

- [Rick and Morty Squirrels](https://youtu.be/ojZVpb0cVkE?si=SN9pzWPDbMIot7IU)

They had chewed through the transformer and are attackin the whole town... 
Who would have guessed we get Unicorn Rabbits and Zombie Squirrels before GTA 6...

- [Unicorn Rabbits ... ](https://youtu.be/hBh3tTC_VuE?si=kU-CPBon43wtDGTx)

There is only one solution at a time like this... 

- [Owl Possession](https://youtube.com/shorts/_cW5aj8D7JI?si=KFuLi_asgeqG54S8)

We will let an owl posses a robot to scare off these vermin...
But we will need to manufactor the idol for this bird to control!

Time for 3D printing!

## Octoprint

It's time to setup [Octoprint](https://youtu.be/HBd0olxI-No?si=Lbe4XvmvI13EZkYW) on a modified Ender 5 so we can deal with these demons.

For this project we have choosen the name: `Celebrimbor` in honor of Lord of The Rings again.

Had to reflash the iso using the Raspberry Pi Imager. Which means we will also have to reconfigure the BL Touch ETC.

- https://all3dp.com/2/octoprint-bltouch-guide/
- https://simplyprint.io/compatibility/creality-ender-5-plus/octoprint-setup

The Last time I worked on this I was editing the actual Marlin marlin files for the firmware for the Ender 5 printer.

- [Ender 5 BL Touch: Marlin](https://www.youtube.com/watch?v=Jmu5Fh_nPtw)

The printer is altered to provide power to the pi directly without a cable. The BL Touch is installed as well.
Just had to assignt the printer a static IP address and get the raspberry pi working again.

But then I ran into my first error. After installing several plugins I had before, I tried to home the device and a kill process is being outputted from the firmware. Might need to recompile the firmware as I was messing with this printer last time I used it.

Instead we are going to go with the much easier option of just using a different printer that I was still working last time I touched it...
That is not what I meant to break...

Anyway, new used printer (aka just a different one from the closet) and we woot woot we are printing a raspberry pi case!
I have a camera watching it as well so my house doesn't burn all the way down...

I'll need to find the custom plugins I wrote and update one of them to be an auto 911. Have a temperature sensor attached to the pi or nearby and if that gets too hot it will ask the camera as well about a fire and then call 911 using Twillio. The plugin currently just uses a free dev account to text me about events on the printer... or well it did... I need to redown that one. I also wrote an auto upload to Google Drive and YouTube as well.

## Local Minimums

There are some problems with the LeetCode generator.

1. It relies on the editorial section.
2. I am generator a random number from 1 to ~ 20 when there are 2K problems which doesn't play well with 1.
3. Too much HTML. The original code assumed that the editorial code would give back all solutions. It appears to use AJAX though.
	a. This is creating too much junk code now and should just have the solutions in plain text.
4. Not adding the Java code solutuions to my actual repo just the html and text information about the problems.

While waiting for the raspberry pi imager to finish writing the SDK, I was able to atleast solve the minimum problem by switching 2.

Now it just uses the button on the LeetCode website to generate a random problem from my TODO list. This is leading to better results because I had developed a sink of problems that no longer had editorials.

The other problems will get solved too. I have code to parse solutions and I have some other ideas about what to do with it as well. 
I thought it might be interesting to use LeetCode problem data to train an LLM for solving AL problems.

I was able to get solutions to non editorial questions as well now. It does assume the answer actually works though instead of searching until it gets one that does.

That is something I could add later. First its time to learn about setting up another NAS.

## NASTY NAS!

First some tunes! Since a NAS is perfect for music and video storage :)
- [I know I can NAS](https://youtu.be/RvVfgvHucRY?si=3ZTXZWaa4srMj00C&t=79)

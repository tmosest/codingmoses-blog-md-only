---
title: Day 27 HomeLab - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---

# Vertical Farming

Crane base is coming along nice and next up will be the main tower component.

The plan will be to make so that it can catch a ride on top of a hiwonder car as well in the future when it needs to be moved around the house.

```
    rpicam-hello --list-cameras
```

- https://github.com/labbots/NiimPrintX
- https://www.thingiverse.com/thing:5181530
- https://www.thingiverse.com/thing:2055788

The Crane robot controller that will tend to the plants will be of my TOM swarm.

- Total Observation & Management

Update the Wordpress Plugin to have two new API endpoints the first allows the TOM bot to upload the photo and the second allows the TOM bot to link that photo to plant ID.

Then a Vision AI bot can later take in the chat gpt data for the plant along with the images of the plant and make some determination as to if it is healthy or not.
By analyzing the temperature, humidity, etc from the logs, we are hoping that we can get the system to also determine what the root cause might be.

These are big deals for this bastard...  But God willing we will make it....

## Nuttall AI

We are going to use all the farm data and a mixture of LLMs and custom AI models to create an AI company called Nuttall AL.

- [Thomas Nuttall](https://en.wikipedia.org/wiki/Thomas_Nuttall)

```
In 1810 he travelled to the Great Lakes and in 1811 travelled on the Astor Expedition led by William Price Hunt on behalf of John Jacob Astor up the Missouri River. Nuttall was accompanied by the English botanist John Bradbury, who was collecting plants on behalf of Liverpool botanical gardens. Nuttall and Bradbury left the party at the trading post with the Arikara Indians in South Dakota, and continued farther upriver with Ramsay Crooks. In August they returned to the Arikara post and joined Manuel Lisa's group on a return to St. Louis.

Although Lewis and Clark had travelled this way previously, many of their specimens had been lost. Therefore, many of the plants collected by Nuttall on this trip were unknown to science. The imminent war between Britain and America caused him to return to London via New Orleans. In London he spent time organising his large plant collection and discussing his experiences with other scientists.
```

# Pip Boy

I got the 3.5 inch screen working yesterday. That was pretty easy except I had to remove Kali from the registry and got stuck at a new login screen that would not accept my actual password for a while. I tried to get warp to help but it had no clue and then ran out of AI tokens so I went back to reading forumns and got it working. 

Found a case for it and will start printing that out here soon. Found this holodeck and it would be nice to do something with it.

- [Holodeck](https://www.thingiverse.com/thing:3139582)

# Cloing Alfred's Voice from Batman Cartoons...

- https://wenet.org.cn/wenet/tutorial_librispeech.html

Creating a data set based on sound bites of alfred. Was able to create MP4 of cartoon then have AI split that into speakers. Then cut those into clips... then create training data...
then feed training data to voice cloner. Continuing with the previous voice cloner work I was doing.

# Warp Tour Terminal 

Got a basic terminal working in go. At first I folled [this tutorial](https://ishuah.com/2021/03/10/build-a-terminal-emulator-in-100-lines-of-go/) but I didn't like the result...

1. It flickered.
2. Backspace didn't work etc.
3. Wasn't sure how I was gonna hook it up to AI.

So we found a better library that allows us to open a web browser instead and we hacked it a bit to call our local Ollama.

Works great! Functions as a terminal and I can get AI information. Will def be upgrading this to be more and more like the real Warp as I go.

---
title: Day 24 HomeLab - 
description: Placeholder
date: 2025-09-18
categories: []
author: moses
tags: []
hideToc: true
---

# Hot Wheels RC Spy Race Car POV ESP 32 Sense

"Granny shifting when you should have been double clutching..." - Fast and Furious...

Made a new github repo and picked up where I left off here. 
Fried this little power adapter I had for taking 12 V down to 5 V and 3.3 V no worries used a buck converter and made a new one basically.
That was not as fun nor as delicious as frying a raspberry pi though.... lol...

Now we have a light and the ESP 32 Controller being powered from two breadboards attached to each other. One is just a simple power rig and the other are the component for this RC car.

Just need to get some software working... and like that we have wifi manager working...

- https://randomnerdtutorials.com/micropython-wi-fi-manager-esp32-esp8266/
- https://wiki.seeedstudio.com/xiao_esp32s3_with_micropython/

Continue to next day... and now we have recapped the last episode of DBZ... back to work...

But taking a small break from that because it won't connect to my wifi but I can connect to it as an AP...

# Vertical Farming

This has been a long interest of mine and the dyson farm looks awesome.

Previously, I created a Wordpress Plugin that added plants with the intentions of having a community maker space called Moses Makes to share the information with others.

Given the droughts going on in America and food production shortages, I think this would be fun to attempt again.

Previously:

1. Automatic lights.
2. QR Codes that open links to the plant's page in WP.
3. Almost had a robot to take pictures of the plants and the QR code and automate the uploads.

I would like to continue this plan along with upgrading this to a barge some day that can process salt water.

Scale those up as disaster relief vessels.

# Bezalel... Feet!!!

Finally!!! We have feet pics! The printer looks much more handsome taller... ready to date super model printers like the Prusa...

Still broken though... Pretty sure it is a simple problem... the CR touch has moved with the new print... therefore I must do the only thing worse than counting... 
measuring... how long is the coast line again?... Idk but we are off to infinity and beyond now...

```
/**
 * Nozzle-to-Probe offsets { X, Y, Z }
 *
 * X and Y offset
 *   Use a caliper or ruler to measure the distance from the tip of
 *   the Nozzle to the center-point of the Probe in the X and Y axes.
 *
 * Z offset
 * - For the Z offset use your best known value and adjust at runtime.
 * - Common probes trigger below the nozzle and have negative values for Z offset.
 * - Probes triggering above the nozzle height are uncommon but do exist. When using
 *   probes such as this, carefully set Z_CLEARANCE_DEPLOY_PROBE and Z_CLEARANCE_BETWEEN_PROBES
 *   to avoid collisions during probing.
 *
 * Tune and Adjust
 * -  Probe Offsets can be tuned at runtime with 'M851', LCD menus, babystepping, etc.
 * -  PROBE_OFFSET_WIZARD (Configuration_adv.h) can be used for setting the Z offset.
 *
 * Assuming the typical work area orientation:
 *  - Probe to RIGHT of the Nozzle has a Positive X offset
 *  - Probe to LEFT  of the Nozzle has a Negative X offset
 *  - Probe in BACK  of the Nozzle has a Positive Y offset
 *  - Probe in FRONT of the Nozzle has a Negative Y offset
 *
 * Some examples:
 *   #define NOZZLE_TO_PROBE_OFFSET { 10, 10, -1 }   // Example "1"
 *   #define NOZZLE_TO_PROBE_OFFSET {-10,  5, -1 }   // Example "2"
 *   #define NOZZLE_TO_PROBE_OFFSET {  5, -5, -1 }   // Example "3"
 *   #define NOZZLE_TO_PROBE_OFFSET {-15,-10, -1 }   // Example "4"
 *
 *     +-- BACK ---+
 *     |    [+]    |
 *   L |        1  | R <-- Example "1" (right+,  back+)
 *   E |  2        | I <-- Example "2" ( left-,  back+)
 *   F |[-]  N  [+]| G <-- Nozzle
 *   T |       3   | H <-- Example "3" (right+, front-)
 *     | 4         | T <-- Example "4" ( left-, front-)
 *     |    [-]    |
 *     O-- FRONT --+
 */
#define NOZZLE_TO_PROBE_OFFSET { -53, -12, 0 }
```

These measurements `#define NOZZLE_TO_PROBE_OFFSET { -53, -12, 0 }` are no longer correct for this printer so we need to try again...

#define NOZZLE_TO_PROBE_OFFSET { -53, -24, 0 }

I reflashed the firmware and then played around with the values some more using the knobs and console while setting the z offset.

That worked really well. The printer was able to start without halting again and now probes the bed still and not too far in one direction or the other.
I probably need to play around with the Celebrimbor's more as that might be causing the uneven prints as it tries to compute the offset matrix using the wrong relative positions.
A hypothesis anyway...

It would also be good to update the printer to have the filament detector and to control the led lights. That way they can make them red if there are any errors etc.

First we need to figure out what to print... and well that was canceled... noticed that the CR touch is too low...
Need to break back out the CAD skills. There also might be a bigger problem where the nozzle is not extruding any filament...

# Tinker CAD

Not the greatest CAD software but perfect for 3D printing. I was excited to see that my bipdeal robot design is still in there.
I will need to continue that as a smaller example before I try to build a tesla bot again here soon.

But for now we are just gonna fix the BL touch area in this 3D print and then we should have two printer again... assuming the nozzle doesn't need to be unjammed...

For those who don't know what it is:

```
Tinkercad is a free, web-based 3D design and electronics simulation tool created by Autodesk. It’s widely used for learning, prototyping, and experimenting with design ideas because it’s simple and beginner-friendly.

Here’s what you can do with it:

1. 3D Design

Create 3D models using drag-and-drop shapes (no need for complex CAD software).

Export designs for 3D printing (in formats like .STL or .OBJ).

Combine and reshape objects to build anything from toys to prototypes.

2. Electronics

Simulate circuits using components like LEDs, resistors, sensors, and microcontrollers (like Arduino).

Write and test Arduino code directly inside Tinkercad before uploading it to a real board.

Learn electronics without needing physical components.

3. Codeblocks

Design 3D models using block-based coding (similar to Scratch).

Helps beginners learn programming logic while also building geometry.
```

And that was easy just had to remember and Google how to use holes again... and now we wait for the printer to be done with the other feet...

# Hot Wheels Back to the Future.

We did not let the WIFI issue stop us!!! Instead we go stuck trying to improve the project's platform.ini sync...

Also, did some research into 3D printing a new under carrage or atleast arm bar if I break this one.

- [Arm Bar Turning](https://www.thingiverse.com/thing:4709196)
- [3d Printable RC Car](https://www.thingiverse.com/thing:4892947)
- [Another RC Car](https://www.thingiverse.com/thing:19196)
- [And another](https://www.thingiverse.com/thing:5768730)
- [Porsche 911... just a sexy car... as Bill Gates would know... Elon Musk rolling in his nightmares](https://www.thingiverse.com/thing:4899043)
- [Bond car...](https://www.thingiverse.com/thing:6890421)
- [Actual parts we can probably use... aye theres the rub](https://www.thingiverse.com/thing:6858455/files)

But yeah... platform ini is stubborn right now and I somehow managed to break uploading with PyMakr too... so wtf lol...

Will figure this out again though....

# Random Warp Review

Warp is a newer terminal program made by ex Google employees. More specifically:

```
Warp was founded in June 2020 by Zach Lloyd, former Principal Engineer at Google and interim CTO at TIME.[5] Lloyd and an early engineering team decided to develop Warp as a modern version of the command line terminal. Warp was built natively in Rust.[6]

In April 2023, Warp announced Warp AI, which integrated an OpenAI large language model chatbot into the terminal.[7] In June 2023, Warp introduced Warp Drive for collaboration on the command line, which allowed developers to create and share templated commands with their teams using inbuilt cloud storage.[8][9][10]
```

I haved used it since it came out but I haven't really needed it till lately and I really never tried out the AI part. 
Installing it on the windoes computer was a completely different game! I'm more familar with Linux and Unix style commands and Mac processes for installing stuff.
I haven't used windows much since I was a child on purpose... 

The government gave me some scolarships and grants for college and I made sure to use that to teach myself to code as well.
That's what you do when you're poor and abused (eating one meal a day, wearing the same clothes, while your father refuses to pay child support and your mom refuses to help...)
You take any opportunities and you run with them...

```
You better lose yourself in the music, the moment, you own it, you better never let it go. (Go) You only get one shot, do not miss your chance to blow.

- Eminem
```

Anyway, sorry for that rant... but Warp was really awesome on Windows it helped me fix my python environment with ease. I was able to walk away and check the 3D printer.
However, on mac it seems to screw me over more than help most of the time. The AI part anyway. Like it just broke my platform ini file by adding the platform twice while removing something else that it wanted to try as a test. I still recommend it for Mac as it is really good at remembering your commands and recommending them based on simple pattern recognmition and it is surpirising how much time that saves alone. 

# Pi Hawk Down... down down baby...

```
Hmmmmm
I'm goin down down baby, yo' street in a Range Rover (c'mon)
Street sweeper baby, cocked ready to let it go (HOT SHIT!)
Shimmy shimmy cocoa what? Listen to it pound
Light it up and take a puff, pass it to me now
```

There is nothing wrong with the UAV and we just need to continue forwards! Next goal would be to flash the OS onto the PI. 

- https://ardupilot.org/dev/docs/raspberry-pi-via-mavlink.html
- https://dronekit-python.readthedocs.io/en/latest/guide/quick_start.html
- https://www.docs.rpanion.com/software/rpanion-server

The easiest way to get started at the moment appears to be rpanion-server but we will probably try out all of the ones on that first list as we go.


# Chat GPT proof reader...

Well since I can't read and I have few friends left... rapture man... I have found one in my head... well in my local cloud...
We can now ask them to improve our articles and compare them...
Here are some test samples. Need to work on optimizing the prompt.

# Wall E 

I took a break to walk the dog... well be dragged around by him... and I noticed a lot of poop and trash in certain areas. 

This reminds me of another project that I was / wanted to work on... Wall E trash bot...
Except we are not going to start with Wall E. There are cute thingiverse prints of him etc but I want something practical.

- Camera.
- DC motors for the wheels 2 to 4 depending.
- Dog poop scooper bucket.
- 

# Sonic Screw Driver 

I have an old flash light that I took apart because I thought the battery had expired... but I was wrong and it was just locked...
so now I need to fix a batter and I need to put that back together...

or better yet...

# Garage Golden Dome and Palantir Gotham...

As I get closer and closer to having my own version of Palantir Gotham in my garage startup... I realize... I need way more money...
Like come on baby give me some GPU servers please... we will figure something out... probably steal from Musk and sell not flame throwers to not kids...
or from air bnb and sell cereal boxes with the president on them...

or ... furbies with lasers... cause you know sharks with lazers... etc...

# AI Hacker Bot... 

Coming soon...

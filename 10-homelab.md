---
title: Day 10 HomeLab - Robots vs Mouse?
description: Setting up a Proxmox container to run VOXEL51 and CVAT for Vision AI training.
date: 2025-09-04
categories: [Yolo, VisionAI, ]
author: moses
tags: [yolo]
hideToc: true
---

# To A She Mouse

Doing laundry today and I saw a mouse in my house... Which reminded me of an old poem that is difficult to read...

- https://en.wikipedia.org/wiki/To_a_Mouse

```
Wee, sleekit, cow'rin, tim'rous beastie,
O, what a panic's in thy breastie!
Thou need na start awa sae hasty,
Wi' bickering brattle!
I wad be laith to rin an' chase thee,
Wi' murd'ring pattle!
```

- https://www.youtube.com/watch?v=LA55FrrhEDk

Not that anyone understands what that means... so lets track a mouse... and some other rodents probably because squirrels ruined my house as well...

Then I can figure out where they live and move in there and refuse to pay rent too muhuhahahaahahahah!!! Or atleast get more nuts in the settlement...

## Farenheit 451

The plan is to stalk them harder than the government does your mom's onlyfan's account...

// TODO image of Futurama naked robot circuits.

Then we will send a swarm of robots after them? (Like those peeping tom drones from ICE ice baby...) 

probably not... cause every robot is just another set of problems... AND

I got 99 problems but a robot aint one!

Plus who can read anymore... so learning robotics is def. out of the question... I am not trying to have my house burned for owning books... thats for nerds :) 

I'm just trying to serve water and play foosball... thank you...

# Yolo V8 / V11 and Animal Detection.

After a refreshing H20 break, it's time to get caught up on YOLO. You only live once so stop reading this and get out there!!!

- https://github.com/shreehari-revankar/WildEye
- https://www.kaggle.com/code/anmolys/animal-detection-using-yolo-v8
- https://www.ultralytics.com/blog/role-of-computer-vision-and-ultralytics-yolo11-in-animal-monitoring
    1. This was interesting because it tried to show fine grained behavior for a creature.
    2. Now I'm just thinking about the CIA viewing us as cattle for the alien overlords.

Oh you're still here... me too... and well I don't know how to feel about so we are going to train an AI model to process this...
and aslo help answer the age old questio of what to feed your girlfriend when she "isnt hungry but was like an hour ago..."

- Ew its science! https://www.sciencedirect.com/science/article/pii/S1574954124003339
    - Bill Bill Bill!!!! Bill Gates the computer guy! Oooooops wrong timeline....

Wait... I meant you only look once (the explanation for all my typos)... 
and the last time I used this wonderful library it was like version 3 or so... 
and not much has changed except its better!!!

YEAH!!!!!! Oh and there are several SASS platforms now for selling AI vision solutions based on YOLO...

`Welcome to the internet! Have a look around. Anything that brain of yours can think of can be found. We've got mountains of content, some better, some worse.`

- https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://www.youtube.com/watch%3Fv%3Dk1BneeJTDcU&ved=2ahUKEwj-2Jnj-7-PAxUYwOYEHWd7FWcQwqsBegQIGBAH&usg=AOvVaw0aADfVZzY3CXHZjbXx1ZHh

Ok back from the ADHD... 

## shreehari-revankar/WildEye 

Time to go down this rabbit hole... and find an elephant... everybody loves elephants...

// TODO insert Elephant image...

```
Our dataset consists of a diverse collection of images showcasing various animals. The YOLO model used in this project has been pretrained on this dataset, enabling it to detect and classify different animal categories. The animals included in our dataset are:

    Bird
    Cat
    Dog
    Horse
    Sheep
    Cow
    Elephant
    Bear
    Zebra
    Giraffe
```

Well those will be awesome for my next visit to the zoo and for my DIY AI birdfeeders later but not for my rodent problem!
Looks like we are training our own dataset guys!

This repo is just trained from COCO128, a subet of the infmaous late great vionsary data set.... COCO...

// TODO insert photo... coco foster's imaginary friends

Here we can learn some more about her likes and dislikes... margharittas on the beach etc... Chcoclate (Starwberry) milk... 
    - Man if I had real friends maybe I could read good and talk right... 
    - https://youtu.be/NQ-8IuUkJJc?si=MGlnaVV5QNwuORW2

- https://docs.ultralytics.com/datasets/detect/coco128/#sample-images-and-annotations
    - Subset of https://cocodataset.org/#home
- https://cs.brown.edu/~gmpatter/pub_papers/coco_attributes.pdf

Ultralytics has a bunch of additional datasets as well. That can useful for other projects in the links above.
As well as an explanation from Brown...

BUT WAIT THERE's MORE!

### VOXEL51

"I'm in love with a voxel... 
great software... 
high quality software... 
the best software ever... 
they don't make software like this anymore...
Teslar shoud really use this... 
it would make their great cars even greater!!!"

- President Donald J. Trump (allegedly but probably just an AI voice actor)

Well here are some links... 

- https://docs.voxel51.com/getting_started/install.html
- https://docs.voxel51.com/getting_started_guides/object_detection/index.html
- https://docs.voxel51.com/dataset_zoo/datasets.html

For those who don't know what VOXEL and CVAT are, here ChatGPT in the bathub naked with a toaster...

```
Alright, let me channel the "naked lady in The Big Short" vibe, breaking it down for you like it's the most straightforward thing in the world. Picture me pointing at the screen, but instead of complex financial terms, I'm showing you computer vision tools.

Voxel51:
Okay, so Voxel51 is like the Netflix for analyzing visual data. Imagine you're running a company that has tons of footage from cameras—could be anything from security cams to drones to self-driving car cameras. Voxel51 helps you manage, analyze, and even understand that footage in a super efficient way. You know how Netflix is like, "Hey, based on what you watched, here's what you'd probably like next"? Well, Voxel51 does that but for visual data. It can figure out what's going on in the footage, track stuff, and even give you insights without you having to sift through hours of boring video.

CVAT (Computer Vision Annotation Tool):
Now, CVAT is basically like the tool you use to "train" a machine to understand what’s happening in an image or video. If you're building a self-driving car, for example, the car needs to know what a "pedestrian" is, right? CVAT helps you label images with tags—like, "Hey, this thing here is a pedestrian, that thing is a car, and this is a stop sign." It's a way to teach AI by giving it the right context for what it sees. You could think of it as a big "labeling party" for computers.

So, Voxel51 gives you insights into how to use and analyze visual data, and CVAT is the tool you use to label and train those machines on how to interpret that data.
```

Ok, got it... netflix and chill with some vision AI ideas and we get these tools... time to spawn a PROXMOX demon baby and put the fear of God into her!

The power of vision AI compels you!!! The power of vision AI compels you!!! 

Never doubt the worm... never doubt the worm...

- https://www.youtube.com/watch?v=AvCI8tBU3EE

And just like that a VM is born... and it even has my eyes... so exciting...

Now on to the good part!

#### Docker baby docker baby docker baby docker...

To the window... Yeah! Yeah!

Still need to have voxel.homelab.com work so we will use the power of docker! To raise it from the dead!
and terraform can't forget about terraform keeping my life together right now...

My main repo gives me the OPNSense, NGINX Proxy, and Proxmox server needed plus we will reserve a mac and IP for the node containing the above services.

The new repo for the docker compose for CVAT and VOXEL51 will have the proxmox instance deploy as well.

This technically ran into 9-5 so I'm going into Blog 11... Assume there are working services now on their own subdomians...

Here are the final ramblings of Frank Sinatra... and Adam Sandler...

#### Ultralytics integration
Adding Predictions using Ultralytics

```
Thanks to FiftyOne’s integration with Ultralytics, we can pass any Ultralytics YOLO model into apply_model as well!

!pip install ultralytics

from ultralytics import YOLO

# YOLOv8
model = YOLO("yolov8s.pt")

# model = YOLO("yolov8m.pt")
# model = YOLO("yolov8l.pt")
# model = YOLO("yolov8x.pt")

# YOLOv5
# model = YOLO("yolov5s.pt")
# model = YOLO("yolov5m.pt")
# model = YOLO("yolov5l.pt")
# model = YOLO("yolov5x.pt")

# YOLOv9
# model = YOLO("yolov9c.pt")
# model = YOLO("yolov9e.pt")
dataset.apply_model(model, label_field="YOLOv8")
```

Note: VOXEL51 makes me want to scan my brain etc... or get a mircroscope and train AI to detect stuff in it.

- https://docs.voxel51.com/tutorials/index.html

Wow I'm in love...

Well now I don't have to create my own boxes or what ever crazy stuff I did in the YOLO UNO project 5 years ago

- https://github.com/tmosest/uno_detection

Well this is pretty cool... 

- https://labelbox.com/solutions/coding-tasks/

This is better! Much better! Shampoo better! No CONDITIONER better...

- https://github.com/cvat-ai/cvat
- https://docs.cvat.ai/docs/administration/basics/installation/
- https://medium.com/voxel51/fiftyone-computer-vision-tips-and-tricks-nov-24-2023-71cd9e88ce54

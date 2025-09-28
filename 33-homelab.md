---
title: Day 33 HomeLab - 
description: Placeholder
date: 2025-09-27
categories: []
author: moses
tags: []
hideToc: true
---

# RC Crane 

Finally!!! We are getting closer to being done with this... had some technical difficulties...

But we recruited some help and now things are getting much better...

The print might actually be done today and then I can assemble it! Even though I'm pretty sure my cat... is dead.. I mean cat nip...

# Exploring More Ollama models

Decided to download some more ollama models now that we have access to some more VRAM.

| Model / Family        | Primary Use / Strengths                                                                         |
| --------------------- | ----------------------------------------------------------------------------------------------- |
| **starcoder2**        | Code generation, handling code-centric tasks (multi-language) ([Ollama][1])                     |
| **vicuna**            | General-purpose chat / conversation, built on LLaMA / LLaMA-2 foundations ([Ollama][1])         |
| **orca-mini**         | Lightweight general model (3B → 70B variants) for lower-resource environments ([Ollama][1])     |
| **llama4**            | Multimodal (vision + text) tasks (e.g. combining images + language) ([Ollama][1])               |
| **deepseek-coder-v2** | A coding / programming-specialized model (Mixture-of-Experts architecture) ([Ollama][1])        |
| **qwen2.5vl**         | Vision-Language (VL) model — handles multimodal inputs (text + images) ([Ollama][1])            |
| **gemma3n**           | Lightweight model family optimized for running on everyday devices (e.g. laptops) ([Ollama][1]) |

[1]: https://ollama.com/library/?utm_source=chatgpt.com "library"

# War Tour Terminal

The command recommendation engine is having some issues and I would like to improve certain flows.

For one Warp is much faster at certain things and if I ask it my ip and mac then it can quickly run the commands to get those things.

Also since my terminal can be access via a web port if desired I would like to be able to simply tell a system or computer to do something and have it run all the commands to install and setup a repo while I walk the dog...

I dont think I will get to solve these things today though beause Otis isn't feeling great and I have other things to do as well.

# Cloning My Voice 

Back to trying to make Alfred again... but first we are going to clone my voice just because I have the training data more ready...

I have all of Book 1 and Book 3 of Psalms and more at my disposal... which is a few hours... and I can easily generate the transcripts because I have a local bible app... plus the words are on the screen so I could also get a script to just parse the text image.

- https://wenet.org.cn/wenet/tutorial_librispeech.html

`for f in *.mov; do ffmpeg -i "$f" -c:v libx264 -c:a aac "${f%.mov}.mp4"; done`

Really just need the wav files but other devices in my house have issues with .mov files

`for f in *.mov; do ffmpeg -i "$f" -vn -acodec pcm_s16le -ar 44100 -ac 2 "${f%.mov}.wav"; done`

Now I got some mp4 files instead of mov files.

... and while that is going ... we write some simple Python to call my API and get the text for each video based on the name of it... aka Psalm 1... etc...

# Mr Mulligan Fashion AI...

```
All along the watchtower
Princes kept the view
While all the women came and went
Barefoot servants, too
Well, uh, outside in the cold distance
A wildcat did growl
Two riders were approaching
And the wind began to howl, hey
```

- Jimmy Hendrix : All Along the Wathtower

The rats came back... because my house has enough C4 cans to blow up a 3rd world country and I am slowing devolving into the next Asmongold lol...

The cat has left me... he has joined my neighbors instead as they have delicious fresh fish...

The worst part is that Otis, my lab, has betrayed me and the home lab! That damn meth lab... I should have known... 
He demands more cheese but I am broke... and I have no government cheddar... French revolution failure I have become...

There is only one solution to this smelly smell that smells kinda smelly and that is to retreat to my cave and become a perfumer...
Except in this cave I found a geni!!!

- [Friend Like Me Aladdin](https://www.youtube.com/watch?v=qsrGi_d-fW8&list=RDqsrGi_d-fW8&start_radio=1)

He said I would get three wishes... my first without hesitation was to solve the Reimann Hypothesis...

He replied, I'm sorry sir but not even I can do that...

How about you help me become the Prince of Egypt instead?...

Well we will need to dress you like one first... then youll need to catch the eye of a princess...

Is that really a possibility for a street rat like me?

- [I'll pull you up by the bootstraps sir](https://youtube.com/shorts/51lf1T2wBbM?si=8K6_V1oqkFE1q2vi)
- [I'll make you great](https://www.youtube.com/shorts/uwx46gRe6A0)
- [I'll make you remeber who you are](https://www.youtube.com/watch?v=hKQLKlHf2gI)
- [I'll give you something to believe in](https://www.youtube.com/watch?v=G5uamDMoW4o&list=RDG5uamDMoW4o&start_radio=1)

and so I replied... I'll make you free from your cage Blue... and you can finally do you... But where do we start?...

and with that I had some cheddar and the rats now camels for this cinderella man... Bravo! Enersto!

- [Time to Grind](https://www.youtube.com/watch?v=4mDBzvCdnPU&list=RD4mDBzvCdnPU&start_radio=1)

Yo Genie take a look at these old and current pics of me and tell me who I look like... I have a feeling you will make some heads turn...

# Computer Scouts?

Want to combine my ForceAI idea for ASVAB career assessment and guidance with this brother sewing maching that I have and make custom badges for people to earn at community centers. Deploy AI learning helpers at libraries.

Spearking of which I want to make a small altar and library in my yard with vision AI that can answer questions. People could watch movies. Charge devices using a solar bank. It would even give you a local lib card for it and a way to checkout out and get books like a vending machine. 

Yahweh's Library of Alexandria... 

# FRED

Seeing President Trump take inventory of the military supplied reminds me of a program I wanted to make a log time ago for logistics....

It was basically like Live AI and what Zuck is working on...

# Melchizedek API 

Turns out I need to make a few updates. 

1. Python script to turn my txt file into JSON.
2. Python script to quickly search it like the express javascript endpoints. 

That was easy but some of the data was messed up and not a 1-for-1 so we also used AI to verify the image and the voice to text.

Now that I have all the wav files and all of the transcripts I need for each file I need to generate the training data.

- https://www.assemblyai.com/blog/end-to-end-speech-recognition-pytorch
- https://github.com/resemble-ai/chatterbox
- https://medium.com/@klintcho/creating-an-open-speech-recognition-dataset-for-almost-any-language-c532fb2bc0cf

Need to do some editing to the trianing data... right now each chapter is its own wav file but really it would better if it was a verse level...

but instead we are gonna break it up based on pauses... and then use that as the training data... and generate some flac files...

etc etc... kinda a Sabbath day... tomorrow came fast...
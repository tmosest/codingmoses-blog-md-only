---
title: Day 21 HomeLab - 
description: Placeholder
date: 2025-09-15
categories: []
author: moses
tags: []
hideToc: true
---

# Upgrading Bezalel / Octoprint / 

This is still going well. Just waiting on the part...

and well that is finally done... so lets see if we can get this up and running...

Also we have a new power supply fan to install as well.

# Olama AI Homelab Server

While rewiring the homelab yesterday, we made an upgrade to include an NVIDIA graphics card. Now we,

    1. Download Olama, docker and Open WebUI.
    2. Update the NPM Proxy manager to point at this bad boy.
    3. Have fun using a homelab version of Chat GPT.

- [ShīnChvën Blog](https://atlassc.net/2024/10/24/how-to-share-ollama-server-through-ip-address-and-port)

That exposes the Ollama model on the network for windows etc.

Then we can just launch a docker instance with OpenWebUI pointed at that server.

- [OpenWebUI Getting Started](https://docs.openwebui.com/getting-started/)

`docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=https://example.com -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main`

Note: don't need to run it with the restart value when testing locally.

We can just replace the url with the ip address of the computer running ollama or the NPM proxy ip. 
I tried both and obviously the ip address was a bit faster.
I'm not sure about this trade off. But now we can deploy this open webui to a builder node or to one of my docker nodes.

We will test if this is true or not...

- [Open Web UI Min req](https://github.com/open-webui/open-webui/discussions/736)

For now we just have a convient way to talk to a local Chat GPT instance.

It is amazing how much these things know. Here is some source code it said Bill Gates wrote...

```
;------------------------------------------------------------------
; Altair BASIC - 8080 assembly source (excerpt)
; (c) 1975 Microsoft – public domain
;------------------------------------------------------------------
; The following routine reads a line of input from the user,
; stores it in a buffer, and then calls the BASIC interpreter
; to parse and execute the line.

READLINE
        LXI H,LINEBUF          ; H register holds address of input buffer
        LXI D,80H              ; D points to the input line (80 bytes)
        LXI B,80H              ; B = 128 bytes buffer size

        MVI C,0                 ; clear character counter
REPEAT
        CALL GETCHAR            ; get one character from the console
        CPI 0DH                 ; check for Enter (CR)
        JZ   ENDREAD
        CPI 7FH                 ; check for Backspace
        JZ   BACKSPACE
        MOV M,C                 ; store char in buffer
        INX H
        INX D
        INX B
        INR C
        CPI 80H                 ; buffer overflow guard
        JNZ  REPEAT
ENDREAD
        XRA A
        STAX D                  ; terminate string with NUL
        CALL INTERPRET          ; call the BASIC line interpreter
        RET

BACKSPACE
        INX H
        INX D
        INX B
        DCR C
        MVI M,0
        JMP REPEAT
```

It did not know about his knew book Source Code and I didn't know about his previous book:

```
... snapshot of Bill Gates’ own book The Road Ahead (1995) ...
```

Really this just opens us up for more task automation as we continue on our robotics journey.

# Red Five / PiHawk / TurboPi

Time to start automating a drone with pi hawk... :) 

The goal is to combine this with vision AI algorithms to create a swarm that can deterct lone gunman, school shooters, and people attempting to hurt / kill public officals etc.

This will requiure a few projects to be done in parallel. 

1. Need to get back to `Voxel51` and my training data. 
2. Need to compare training models locally vs the cloud. 
3. Need to experiment more with sending images from swarms back to vision servers.

Luckily, we have 4 or have 5 robot who have volunteered as tribute...

The first few are ground vehicles. That can be used to drive around (under my house for an inspection...)

2 of these are from Hiwonder and use their SDK. I found a smaller version of it falled fast-sdk.

I took that and started updating it. Meet `Uruk-hai` a library for this little hiwonder cars that makes it easier to do stuff with them.

`Lurtz` will be our first robot. [img](https://www.robotshop.com/cdn/shop/files/cb1962f9_001.webp?v=1750409369&width=500)

Previously we were able to assemble him and...

# Job Seeker...

Well being unemployed is still fun but I need funds for the war chest... several jokes to be made here but skipping them...

It is time for the next step in jon seeking... the recruiters are on to me and know I am really just a robot from the future...

They have replied via email.... ewwwww...... talking to people.... but I am an engineer so we need an agent to talk to them for me...

- [Meet our new AI secretary... Joke](https://www.youtube.com/watch?v=hNuu9CpdjIo)
- [Medium How to Setup what we did above](https://medium.com/open-webui-mastery/build-your-local-ai-from-zero-to-a-custom-chatgpt-interface-with-ollama-open-webui-6bee2c5abba3)
- [Medium How to Create Knowledge Bases](https://medium.com/open-webui-mastery/open-webui-tutorial-supercharging-your-local-ai-with-rag-and-custom-knowledge-bases-334d272c8c40)
- [n8n documentation on how to create agents](https://docs.n8n.io/integrations/creating-nodes/overview/)
- [This was helpful for some errors I was having](https://community.n8n.io/t/error-while-executing-workflow/28560/6)

`WEBHOOK_TUNNEL_URL to WEBHOOK_URL and set N8N_PUSH_BACKEND=sse`

Forweb sockets:

```
server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name n8n.happybunnies.com;

    ssl_certificate [PATH REDACTED]/cert.pem;
    ssl_certificate_key [PATH REDACTED]/key.pem;
    root /antlets/n8n/var/www;
    location ~ ^/(?!(.well-known)) {
        proxy_pass http://10.1.1.10:5000;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection '';
        #proxy_set_header Host #host;
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;
        # note the inclusion of the protocol 'https'
         add_header Access-Control-Allow-Methods "POST, GET, OPTIONS";
         add_header Access-Control-Allow-Headers "Origin, Authorization, Accept";
         add_header Access-Control-Allow-Credentials true;
         add_header Access-Control-Allow-Origin *;
#        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
#        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';

    }
}
```

// TODO add my docker compose and NGINX settings.

```
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Host $host;
proxy_set_header X-Forwarded-Proto $scheme;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Origin "https://n8n.lab.duckdns.org"; # Set the Origin header
    
```

Also need a custom location for the websocket or the agent will throw an error.

```
    # Required for WebSocket and Server-Sent Events (SSE)
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_http_version 1.1;

    # Explicitly set headers for correct routing
    proxy_set_header Host $host;
    proxy_set_header Origin https://$host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
```

Base docker-compose.yaml

```
    version: '3.8'
    services:
      n8n:
        image: n8nio/n8n
        container_name: n8n
        restart: unless-stopped
        ports:
          - "5678:5678"
        environment:
          - N8N_PROTOCOL=https
          - N8N_HOST=n8n.example.com
          - N8N_PORT=5678
          - WEBHOOK_URL=https://n8n.example.com/
          - N8N_EDITOR_BASE_URL=https://n8n.example.com/
          # Other n8n environment variables as needed

```

- [Explanation of Goolge API Scopes](https://community.n8n.io/t/google-workspace-admin-oauth2-api-4-scopes/44985)

... Well got API calls to Google working!!! and can parse data in the pipeline... Just need to figure out my flow... 
I'm beginning to feel like a VM God... 

# Pip Boy

Now that we have wikipedia, the next step would be a voice assistant AI.

- [Community Form](https://community.home-assistant.io/t/raspberry-pi-as-a-ha-voice-assist-chapter-4-satellite/632671)
- [Raspberry Pi Home Assistant](https://www.xda-developers.com/raspberry-pi-voice-assistant-how/)


We are going to:

1. Generate a custom wake word model

Create a free account on the Picovoice Console.

Use the console to train your own custom wake word model. Follow the instructions to record your voice saying the desired phrase (e.g., "Hey, Raspberry").

Download the .ppn model file, ensuring you select the correct platform (e.g., linux_arm64 for a Raspberry Pi 5). 

2. Set up the Python environment on your Raspberry Pi 

```
# Update system
sudo apt update && sudo apt upgrade -y

# Install dependencies and create a virtual environment
sudo apt install python3-pip -y
python3 -m venv venv
source venv/bin/activate

# Install Voice AI dependencies.
pip install pvporcupine openai-whisper

```

Some python:

```
import pvporcupine
import whisper
import pyaudio
import wave

# Replace with your Picovoice AccessKey and model path
access_key = "YOUR_PICOVOICE_ACCESS_KEY"
model_path = "PATH_TO_YOUR_CUSTOM_WAKE_WORD.ppn"

# Replace with your desired Whisper model (tiny.en for best performance on Pi)
whisper_model = whisper.load_model("tiny.en")

porcupine = pvporcupine.create(
    access_key=access_key,
    keyword_paths=[model_path]
)

pa = pyaudio.PyAudio()
audio_stream = pa.open(
    rate=porcupine.sample_rate,
    channels=1,
    format=pyaudio.paInt16,
    input=True,
    frames_per_buffer=porcupine.frame_length)

print("Listening for wake word...")

def record_audio(filename, duration=5):
    """Records audio for a specified duration after wake word detection."""
    chunk = 1024
    format = pyaudio.paInt16
    channels = 1
    rate = 16000
    p = pyaudio.PyAudio()
    stream = p.open(format=format,
                    channels=channels,
                    rate=rate,
                    input=True,
                    frames_per_buffer=chunk)
    print("Wake word detected! Recording...")
    frames = []
    for _ in range(0, int(rate / chunk * duration)):
        data = stream.read(chunk)
        frames.append(data)
    
    print("Recording finished.")
    stream.stop_stream()
    stream.close()
    p.terminate()
    
    wf = wave.open(filename, 'wb')
    wf.setnchannels(channels)
    wf.setsampwidth(p.get_sample_size(format))
    wf.setframerate(rate)
    wf.writeframes(b''.join(frames))
    wf.close()

try:
    while True:
        pcm = audio_stream.read(porcupine.frame_length)
        result = porcupine.process(pcm)
        
        if result >= 0:
            print("Wake word detected!")
            record_audio("command.wav")
            
            # Transcribe the recorded audio with Whisper
            result = whisper_model.transcribe("command.wav")
            print("Whisper Transcription: ", result["text"])
            
            print("\nListening again...")

except KeyboardInterrupt:
    print("Shutting down...")

finally:
    audio_stream.close()
    pa.terminate()
    porcupine.delete()

```

How to setup a bootscript

```
# Create the autostart directory if it doesn't exist
mkdir -p ~/.config/autostart

# Create a desktop entry file
nano ~/.config/autostart/whisper_trigger.desktop

```

Add the following content to the file, replacing /home/pi/path/to/ with your actual file path.

```
[Desktop Entry]
Name=Whisper Trigger
Exec=/bin/bash -c "cd /home/pi/path/to/ && source venv/bin/activate && python whisper_trigger.py"
Type=Application
```

Now we can continue this pattern to connect through to anything.

Or we can go with the better option...

Method 2: Use openWakeWord with Home Assistant (advanced)

But we are going to run Home Assistant in a python virtual library instead of just using [raspberry imager to flash it.](https://www.home-assistant.io/installation/raspberrypi)

```
    sudo apt update
    sudo apt install python3-dev python3-pip python3-venv build-essential libffi-dev cargo
    
    sudo useradd -rm homeassistant -G dialout,gpio,i2c # Add groups as needed for hardware access
    sudo mkdir /srv/homeassistant
    sudo chown homeassistant:homeassistant /srv/homeassistant

    sudo -u homeassistant -H -s
    cd /srv/homeassistant
    python3 -m venv .
    source bin/activate

    pip install --upgrade pip
    pip install wheel

    pip install homeassistant

    hass
```
This will start Home Assistant, and it will typically be accessible in your web browser at http://<your_device_ip>:8123.

Will probably need pyenv.
```
Installation Steps:
Install System Dependencies.
Open a terminal on your Raspberry Pi and install the necessary build dependencies. This command covers many common requirements:
Code

    sudo apt-get update
    sudo apt-get install --yes build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libgdbm-dev lzma lzma-dev tcl-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev wget curl make openssl
install pyenv.
Use the pyenv-installer script to install pyenv:
Code

    curl https://pyenv.run | bash
Configure Shell Environment.
Add pyenv to your shell's configuration file (e.g., ~/.bashrc or ~/.zshrc) to ensure it's loaded when you open a new terminal.
Code

    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(pyenv init --path)"' >> ~/.bashrc
    echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
Then, apply the changes to your current shell session:
Code

    exec $SHELL
Install Python Versions.
You can now install desired Python versions using pyenv. First, list available versions:
Code

    pyenv install --list
Then, install a specific version, for example, Python 3.10.0:
Code

    pyenv install 3.10.0
```

Important Considerations:
- Deactivating the virtual environment: To exit the virtual environment, simply type deactivate in the terminal.
- Upgrading Home Assistant: When upgrading Home Assistant, activate the virtual environment first, then use pip install --upgrade homeassistant.
- Dependencies: If you encounter errors related to missing libraries (e.g., cryptography), you may need to install system-level dependencies before installing the Python packages within the virtual environment.
- Configuration: Home Assistant will create a configuration directory (typically ~/.homeassistant for the user running it, or /srv/homeassistant/.homeassistant in the example above) where you can customize your setup.

After we get all of that working we can continue with our custom startup words again...

For a more robust smart-home setup, you can combine the open-source wake-word engine openWakeWord with Whisper via a Home Assistant voice assistant pipeline. This is more complex but offers seamless integration with other smart devices. 

Set up Home Assistant on your Raspberry Pi.

Install the Wyoming integration within Home Assistant.

Add the openWakeWord component and install a faster-whisper component as described in the Home Assistant documentation.

Upload your custom .tflite wake-word model to your Home Assistant server in the /share/openwakeword directory.

Configure a new voice assistant pipeline to use your custom wake word and the Whisper speech-to-text service. 

- [How to Blog](https://www.xda-developers.com/raspberry-pi-voice-assistant-how/)

Well that was more of an adventure than expected due to python version problems...

Also Home Assistant wants a paid subscription to talk to Alexa and Google Home API... I might just be going back to my original python code...

```
There are two sides to integrating Alexa into Home Assistant.

Controlling Home Assistant entities via Alexa:
Amazon Alexa Smart Home Skill - Home Assistant
Complicated initial setup, but easier to use day-to-day since the entities are “in” Alexa just like they are if you use HA Cloud.
Emulated Hue - Home Assistant
Easier initial setup, limited functionality, more follow-up to add entities to be controlled by Alexa
Controlling Alexa devices via Home Assistant (making announcements, playing media, activating skills, running routines you already have in the Alexa app):
Alexa Media Player

Or simply use the ‘Matter Hub’ add-on. It works well.
```

We will continue to play around and find out.

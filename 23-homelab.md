---
title: Day 21 HomeLab - 
description: Placeholder
date: 2025-09-17
categories: []
author: moses
tags: []
hideToc: true
---

# Upgrading Bezalel... 

Well those feet pics got us now where. They didn't want to stick to the board...

Come back to this later.

# Kali Linux (katoolin) Massive Refactor

Got Kali linux working on it but needed to fork and update this repo to Python3.

- https://github.com/tmosest/katoolin

This code is very difficult to read... so we are gonna clean it up some...

- [Refactor](https://github.com/tmosest/katoolin/commit/f795a8e3d79764934febb09f430cfa5fcaf3ef69)

That is much better... for now... the main file was almost 2K lines long and I was getting lost in it...

Now each category is just a list of tools:

```
# Note this is actully all the tools the code has been updated in the repo.

TOOLS_STR = "acccheck ace-voip amap automater braa casefile cdpsnarf cisco-torch cookie-cadger copy-router-config dmitry dnmap dnsenum dnsmap dnsrecon dnstracer dnswalk dotdotpwn enum4linux enumiax exploitdb fierce firewalk fragroute fragrouter ghost-phisher golismero goofile lbd maltego-teeth masscan metagoofil miranda nmap p0f parsero recon-ng set smtp-user-enum snmpcheck sslcaudit sslsplit sslstrip sslyze thc-ipv6 theharvester tlssled twofi urlcrazy wireshark wol-e xplico ismtp intrace hping3 bbqsql bed cisco-auditing-tool cisco-global-exploiter cisco-ocs cisco-torch copy-router-config doona dotdotpwn greenbone-security-assistant hexorbase jsql lynis nmap ohrwurm openvas-cli openvas-manager openvas-scanner oscanner powerfuzzer sfuzz sidguesser siparmyknife sqlmap sqlninja sqlsus thc-ipv6 tnscmd10g unix-privesc-check yersinia aircrack-ng asleap bluelog blueranger bluesnarfer bully cowpatty crackle eapmd5pass fern-wifi-cracker ghost-phisher giskismet gqrx kalibrate-rtl killerbee kismet mdk3 mfcuk mfoc mfterm multimon-ng pixiewps reaver redfang spooftooph wifi-honey wifitap wifite apache-users arachni bbqsql blindelephant burpsuite cutycapt davtest deblaze dirb dirbuster fimap funkload grabber jboss-autopwn joomscan jsql maltego-teeth padbuster paros parsero plecost powerfuzzer proxystrike recon-ng skipfish sqlmap sqlninja sqlsus ua-tester uniscan vega w3af webscarab websploit wfuzz wpscan xsser zaproxy burpsuite dnschef fiked hamster-sidejack hexinject iaxflood inviteflood ismtp mitmproxy ohrwurm protos-sip rebind responder rtpbreak rtpinsertsound rtpmixsound sctpscan siparmyknife sipp sipvicious sniffjoke sslsplit sslstrip thc-ipv6 voiphopper webscarab wifi-honey wireshark xspy yersinia zaproxy cryptcat cymothoa dbd dns2tcp http-tunnel httptunnel intersect nishang polenum powersploit pwnat ridenum sbd u3-pwn webshells weevely casefile cutycapt dos2unix dradis keepnote magictree metagoofil nipper-ng pipal armitage backdoor-factory cisco-auditing-tool cisco-global-exploiter cisco-ocs cisco-torch crackle jboss-autopwn linux-exploit-suggester maltego-teeth set shellnoob sqlmap thc-ipv6 yersinia beef-xss binwalk bulk-extractor chntpw cuckoo dc3dd ddrescue dumpzilla extundelete foremost galleta guymager iphone-backup-analyzer p0f pdf-parser pdfid pdgmail peepdf volatility xplico dhcpig funkload iaxflood inviteflood ipv6-toolkit mdk3 reaver rtpflood slowhttptest t50 termineter thc-ipv6 thc-ssl-dos acccheck burpsuite cewl chntpw cisco-auditing-tool cmospwd creddump crunch findmyhash gpp-decrypt hash-identifier hexorbase john johnny keimpx maltego-teeth maskprocessor multiforcer ncrack oclgausscrack pack patator polenum rainbowcrack rcracki-mt rsmangler statsprocessor thc-pptp-bruter truecrack webscarab wordlists zaproxy apktool dex2jar python-distorm3 edb-debugger jad javasnoop jd ollydbg smali valgrind yara android-sdk apktool arduino dex2jar sakis3g smali"

TOOLS_DATA= ToolData("Information Gathering", TOOLS_STR, 
                     {4: "wget http://www.morningstarsecurity.com/downloads/bing-ip2hosts-0.4.tar.gz && tar -xzvf bing-ip2hosts-0.4.tar.gz && cp bing-ip2hosts-0.4/bing-ip2hosts /usr/local/bin/"
                      , 35: 'echo ntop is unavailable'})
```

The `ToolData` class handles changing that into a list and giving install commands. If something needs a special install then we just add it to the map with the index.
That work much better and then makes the main file code much cleaner...

This selection mapping to tool data and funciton to handle the user input now does all of the heavy lifting that thousands of copied and pasted sections did before.

```
TOOL_CAT_MAP = {
    "1": TOOLS_DATA,
    "2": VUL_DATA,
    "3": WIRELESS_ATTACK_DATA,
    "4": WEB_ATTACKS_DATA,
    "5": SPOOFIND_DATA,
    "6": REPORTING_DATA,
    "7": ACCESS_DATA,
    "8": EXPLOIT_DATA,
    "9": Forensics_DATA,
    "10": STRESS_DATA,
    "11": PASSWORD_DATA, 
    "12": REVERSE_DATA,
    "13": HARDWARE_DATA,
    "14": MISC_DATA
}

def handle_options(opcion1):
    if opcion1 in TOOL_CAT_MAP:
        tool_data = TOOL_CAT_MAP[opcion1]
        print (tool_data.TOOLS_MENU())
        print (TOOL_PROMPT)
        opcion2 = run_eval(OPTION_1)

        if is_integer(opcion2) and 0 <  int(opcion2) and int(opcion2) <= len(tool_data.TOOLS_ARR):
            tool_data.install_tool_by_index(opcion2)
        elif opcion2 == "back":
            # Go back instead.
            return True
        elif opcion2 == "gohome":
            # Go Home instead.
            return None		
        elif opcion2 == "0":
            tool_data.install_all()
        else:
            print (INVALID_COMMAD)

    return False
```

Code can always be cleaner. If GitHub doesn't want to show the code removed then you know it was a good refactor for that file... Hopefully lol... 
This package didn't really have any tests...

## Fixed More

I knew I had made some breaking changes in that refactor so I went back and redid it.

```
TOOLS_DATA= ToolData("Information Gathering", TOOLS_STR, 
                     {4: "wget http://www.morningstarsecurity.com/downloads/bing-ip2hosts-0.4.tar.gz && tar -xzvf bing-ip2hosts-0.4.tar.gz && cp bing-ip2hosts-0.4/bing-ip2hosts /usr/local/bin/"
                      , 35: 'echo ntop is unavailable'})
```

One main problem being I missed some tools from old menu vs copy the install all command and it would break the indexes above anyway. I added back those tools and updated the above code to use string indexes instead.

```
               {'bing-ip2hosts': "wget http://www.morningstarsecurity.com/downloads/bing-ip2hosts-0.4.tar.gz && tar -xzvf bing-ip2hosts-0.4.tar.gz && cp bing-ip2hosts-0.4/bing-ip2hosts /usr/local/bin/"
                      , 'ntop': 'echo ntop is unavailable'})
```

Making it much easier to remember... plus who wants to count...

- https://github.com/tmosest/katoolin/commit/878589458ac410d15db15d8dab18225933535409

# Voice Clone

There are several project ideas that focus on cloning a person's voice.

I have a good amount of data of my voice with it tied to writted text that I am hoping can be used to do this.

`pip install pip-upgrader` That was handy!

```
To all those that are struggling with Apple M1 installation, here is a working solution, specifically addressing the problem of installing the pixellib library that depends on PyQt5 but you can apply it equally to other libs:

PyQt5 is not supported on Apple M1, it needs qt6: https://www.reddit.com/r/learnpython/comments/o4w1ut/comment/h2jele3/?utm_source=share&utm_medium=web2x&context=3 , https://www.qt.io/product/qt6
this means you need to install PyQt6: python3 -m pip install PyQt6
go to the lib you need, in my case pixellib: https://pypi.org/project/pixellib/#files and
download the wheel file
get the wheel tool: pip install wheel
unpack the wheel wheel unpack pixellib-0.7.1-py3-none-any.whl
Change its dependency of PyQt5 to PyQt6
edit pixellib-0.7.1/pixellib-0.7.1.dist-info/METADATA
pyQt5 => pyQt6
pack it back wheel pack pixellib-0.7.1
install it: pip install pixellib-0.7.1-py3-none-any.whl
test in python: `
# should work
import pixellib
P.S. thanks to Terra and ChaOS for supporting work on the project underlying this report.
```

- https://stackoverflow.com/questions/70961915/error-while-installing-pytq5-with-pip-preparing-metadata-pyproject-toml-did-n
- https://sforaidl.github.io/Neural-Voice-Cloning-With-Few-Samples/
- https://ai.stackexchange.com/questions/39386/open-source-vocal-cloning-speech-to-speech-neural-style-transfer
- https://arxiv.org/pdf/1806.04558
- https://www.youtube.com/watch?v=-O_hYhToKoA
- https://github.com/SforAiDl/Neural-Voice-Cloning-With-Few-Samples
- https://drive.google.com/file/d/1tV6Wgnsizd5MXZE9euGUhgIcv6KYu_3z/view
- https://github.com/Talish-wiz/Voice-Cloning/tree/master
- https://colab.research.google.com/github/Talish-wiz/Voice-Cloning/blob/master/Voice_Cloning.ipynb?authuser=1#scrollTo=AIKTS9jUKokS
- https://github.com/IAHispano/Applio
- https://mvsep.com/en

That was really cool... the demo_cli.py works now with python 3.9... tried it with some sort chapter readings and then also about 1/3 of psalms.
Seems like the demo eventually crashes from running out memory if you keep picking a file and generating a voice over and over again.

It was still impressive for how little time it needed to train. It sounds much better when the text matches things in the source.

- https://github.com/tmosest/Real-Time-Voice-Cloning/blob/master/demo_cli.py

# Training a Custom Wake Word

Next goal is to turn a Furby into an Alexa basically.... We have already acheived this with a raspberry pi and picovoice.
It was easy to setup both python scripts and the home assistant os.
I ended up liking the scripts better surprisling. 
There are still several things to be done there too.

Instead of that I would like to train my own wake word model and get it to run on an ESP 32. Then put that in a furby and make them cooler!

Hopefully have them talk to the server and detect other furbies with vision AI and make yo mama jokes but about Yo Furby instead...

- https://www.reddit.com/r/selfhosted/comments/t0fw5z/make_your_own_custom_wakeword_and_other_foss/
- https://github.com/secretsauceai/secret_sauce_ai 
- https://github.com/secretsauceai/secret_sauce_ai/wiki/Wakeword-Project
- https://github.com/secretsauceai/wakeword-data-collector
- https://github.com/secretsauceai/precise-wakeword-model-maker

Ok so it looks like I need to record some version of myself saying the wake phrase and then also have recordings of me just talking and background noise etc so it can learn the diff.



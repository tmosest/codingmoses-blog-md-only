---
title: Day 11 HomeLab - I Fought the Mouse and the Mouse won...
description: Part 2 of Creating a Vision AI system to detect rodents.
date: 2025-09-05
categories: [Yolo, VisionAI, ]
author: moses
tags: [yolo]
hideToc: true
---

# Ozymandias

Back to becoming the Hitler of Rodent detection... the fact that the Chat GPT changed Hitler to Guru scares me...
Note: I was just being sarcastic and "crazy" I'm just a spin off and Grok and Emimem lyrics but Chat GPT is planning the halocaust over there...

```
I met a traveller from an antique land,
Who said—“Two vast and trunkless legs of stone
Stand in the desert. . . . Near them, on the sand..
```

But this time we have learned the error of our ways and instead of exterminating them in genocide...
We will open businesses with them!!! Restuarants to be specific...

// TODO pixar picture...

But first we still need to track them down and learn more about them...
Because there are a lot of false rumors going around thanks to Bird propoganda...

## I want to put on my my my my my 

Boogie shoes... and Boogie with you! 

Now that we have a small army of virtual machines dancing to the Eru Ilúvatar beat we can compose this sweet Symfony.
No! Bad PHP... stay in the closet... no one writes PHP anymore... even Facebook hacked you away and changed their name to Meta to avoid you...

So yeah docker-compose for VCAT... they have a bunch of them explained in their repo that is linked in the install guide.

- https://docs.cvat.ai/docs/administration/basics/installation/

Just:

1. Clone https://github.com/cvat-ai/cvat.git
2. `export CVAT_HOST=FQDN_or_YOUR-IP-ADDRESS` well I added this to the docker-compose.yml file in the repo.
    - Well I actually made a repo. Used the woodpecker and terraform configs to generate the host and deploy my slight diff config.
3. `docker compose up -d`

Well probably without `-d` the first time to make sure it works ok without errors.

```
Half sunk a shattered visage lies, whose frown,
And wrinkled lip, and sneer of cold command,
Tell that its sculptor well those passions read
Which yet survive, stamped on these lifeless things,
```

Woot woot we got containers and data annotation tools! Now what??????.....
like seriously... its 3AM.. the mice are pounding at the locked bedroom door and we need HTTPS so they don't know what we are doing shhhh....

```
Deploy secure CVAT instance with HTTPS
Using Traefik, you can automatically obtain a TLS certificate for your domain from Let’s Encrypt, enabling you to use HTTPS protocol to access your website.

To enable this, first set the CVAT_HOST (the domain of your website) and ACME_EMAIL (contact email for Let’s Encrypt) environment variables:

export CVAT_HOST=<YOUR_DOMAIN>
export ACME_EMAIL=<YOUR_EMAIL>
Then, use the docker-compose.https.yml file to override the base docker-compose.yml file:

docker compose -f docker-compose.yml -f docker-compose.https.yml up -d
```

- https://docs.cvat.ai/docs/administration/basics/installation/

That's an easy enough change to make to the repo. Deploy a diff docker compose and use those two values...

// TODO add image of CVAT...

The Mice!!!! They are here under the floor boards!!!

### Jupiter Notebook Server

Lets create a quick python environment for doing some easy stuff. More than likely I'll just train in Google Cloud but it's nice to have environments like this.

```
Jupyter Notebooks can be effectively run within a Proxmox environment by deploying them in a virtual machine (VM) or a Linux Container (LXC). This setup allows for dedicated resources and isolation, which can be beneficial for data science and development workflows.
Steps for deploying Jupyter Notebook in a Proxmox VM/LXC:
Create a VM or LXC in Proxmox:
Navigate to your Proxmox web interface.
Create a new virtual machine or Linux container, selecting your preferred Linux distribution (e.g., Ubuntu, Debian).
Allocate sufficient resources (CPU, RAM, storage) for your Jupyter Notebook workload.
Install necessary software within the VM/LXC:
Connect to the VM/LXC via SSH or the Proxmox console.
Update the package list:
Code

        sudo apt update
Install Python and pip (if not already present):
Code

        sudo apt install python3 python3-pip
Install virtualenv to manage project dependencies.
Code

        pip install virtualenv
Set up a virtual environment and install Jupyter:
Create a dedicated directory for your Jupyter projects.
Create and activate a virtual environment within that directory:
Code

        virtualenv myenv
        source myenv/bin/activate
Install JupyterLab or Jupyter Notebook within the activated environment:
Code

        pip install jupyterlab
Launch Jupyter Notebook and configure access:
Launch JupyterLab or Jupyter Notebook:
Code

        jupyter lab --ip 0.0.0.0 --port 8888 --no-browser &
--ip 0.0.0.0 makes it accessible from other machines on the network.
--port 8888 specifies the port (you can change this).
--no-browser prevents it from attempting to open a browser on the server.
-- & detaches your process from it.
Note the URL and token provided in the console output.
Access Jupyter Notebook from your local machine's browser using the IP address of your Proxmox VM/LXC and the specified port (e.g., http://<VM_IP_ADDRESS>:8888/). You will need the token for authentication.
```

It is best to make this a part of the infrastructure code in terrashire as well...

# Captains Log

Captains log... the mice have won... they have chewed through the power box and electric lines...

My phone is the hotspot allowing for communication with the outside world...

Solar charger helps little in the pacific north west where sunlight refuses to wander...

I am stuck... I have failed... only one way out... must escape to South American in a secret sub and join the cartel...

- https://www.youtube.com/watch?v=9GQoHIBDogU

or to space?

```
The hand that mocked them, and the heart that fed;
And on the pedestal, these words appear:
My name is Ozymandias, King of Kings;
Look on my Works, ye Mighty, and despair!"
Nothing beside remains. Round the decay
Of that colossal Wreck, boundless and bare
The lone and level sands stretch far away.
```

Note: The tech is factual the other content is less serious.

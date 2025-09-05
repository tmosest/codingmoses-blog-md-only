---
title: Day 8 HomeLab - Astro Blog, Terraform, and Terrashire.
description: Setting back up OPNSense
date: 2025-09-02
categories: [Homelab, OPNSense, Proxmox, Terraform, Ghost, Woodpecker, Astro]
author: moses
tags: [homelab, astro, proxmox, ghost, opnsense, terraform, woodpecker]
hideToc: true
---

# Personal Blog, Terrashire

It has been a while since I have been able to blog. 

I used to own tmosest.com and would work on it while I was employed at Anthem / Elevance Health.

Life quickly took over and I was not able to continue working on it as much as I would like.
I came from a Wordpress background.
It was time for some research into alternatives.

While working at Amazon / IMDb a coworker had told someone about Ghost Blog while I was in hears reach.
I decided to give that a bit of a try this time but was dissapointed.
It was more work and overhead than needed. Ghost is a good alternative to Wordpress from my understanding because it is more performant.
However, what I really wanted was a static site generator like Jekyll. 


```
Jekyll: The Static Site Generator
Jekyll is a tool that takes content written in a markup language (like Markdown) and combines it with templates and other assets to produce a complete, static website. 
Key features:
Static sites: Unlike dynamic, database-driven sites (like those from WordPress), Jekyll sites consist of fast, secure, pre-built HTML, CSS, and JavaScript files.
GitHub Pages: Jekyll is the technology behind GitHub Pages, a popular way to host sites for free directly from a GitHub repository.
Assets: It can process and compile assets, including Sass/SCSS files, into standard CSS. 

```

Found a [great article](https://medium.com/@davidmles/comparing-ssgs-static-site-generators-for-my-blog-a6724a719150) that compared various static site generators.
I ended up going with [Astro](https://docs.astro.build/en/getting-started/). 
I had used Hugo and Jekyll before so I wanted to try Astro as another motivator.

Here are several articles I read about ghost and kub etc. Which is why I went with a simple static site. I'll set kub later for a more complex API.

- https://github.com/sredevopsorg/ghost-on-kubernetes
- https://dev.to/mihailtd/set-up-a-kubernetes-cluster-in-under-5-minutes-with-proxmox-and-k3s-2987
- https://noted.lol/self-host-ghost/
- https://www.allisonthackston.com/articles/local-docker-registry.html
- https://www.plural.sh/blog/kubernetes-on-proxmox-guide/
- https://github.com/khanh-ph/proxmox-kubernetes
- https://kubernetes.io/docs/home/
- https://docs.k3s.io/quick-start
- https://unixorn.github.io/post/hass/2023-07-09-set-up-nginx-proxy-manager/
- https://github.com/khuedoan/homelab/blob/master/README.md
- https://argo-cd.readthedocs.io/en/stable/getting_started/
- https://kustomize.io/
- https://helm.sh/docs/intro/using_helm/
- https://avishayil.medium.com/dynamic-dns-using-aws-route-53-60a2331a58a4

The next step was to find an Astro theme which was pretty simple. 
I found a nice one set outside with a dark and light mode that was free and went with it.

Here is a copy of it.

https://github.com/astrogon/astrogon

I just copied over the files I needed using the command line and cleaned up some of the sample data.

# Sample Astro Deploy

The next step would be deploying the thing to my homelab to get closer to having it outside as a public site.

I found several examples online and gemini also provided some sample code below.

- https://raphaelkabo.com/blog/astro-sourcehut-deployment/
- https://systhoughts.com/automating-astro-website-deployment-with-ci-woodpecker-and-ssh/
- https://docs.astro.build/en/guides/routing/

This is a basic outline for a woodpecker ci .yaml file for building and deploying.

```
kind: pipeline
name: default

steps:
  - name: install_dependencies
    image: node:latest
    commands:
      - npm install

  - name: build_astro
    image: node:latest
    commands:
      - npm run build

  - name: deploy_to_host
    image: alpine/git # Or a specific deployment tool image
    commands:
      - # Your deployment commands here, e.g., using rsync or a cloud provider's CLI
      - echo "Deployment complete!"
```

This was the sample docker file I found for Astro. 
The main issue was that this is a static site and therefore "./dist/server/entry.mjs" DNE

```
# Example for SSR Astro site
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
CMD ["node", "./dist/server/entry.mjs"]
```

Instead we use NGINX again to just serve the static HTML files.

`Dockerfile`

```
FROM node:lts AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine AS runtime
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 8080
```

This means that we will need a new folder and file due to `./nginx/nginx.con`

```
worker_processes  1;

events {
  worker_connections  1024;
}

http {
  server {
    listen 8080;
    server_name   _;

    root   /usr/share/nginx/html;
    index  index.html index.htm;
    include /etc/nginx/mime.types;

    gzip on;
    gzip_min_length 1000;
    gzip_proxied expired no-cache no-store private auth;
    gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    error_page 404 /404.html;
    location = /404.html {
            root /usr/share/nginx/html;
            internal;
    }

    location / {
            try_files $uri $uri/index.html =404;
    }
  }
}
```

Now we can packe up and test the application using the below command and then visting localhost:8080

`docker build -t codingmoses-astro .`

`docker run -d -p 8080:8080 codingmoses-astro`

TODO: insert image

We got an image so now we just need to deploy it and get things working on a proxmox instance.

# Woodpecker Astro Deploy

We will continue this trend and use woodpecker to build the site and then deploy it.

We will build the docker instance.

```
build:
    image: docker:latest
    privileged: true
    commands:
      # Build the Docker image for the Flask application
      - whoami
      # Note these should really be build secrets.  
      - docker login $GITEA -u $USER -p $PSSWD
      - docker build -t $GITEA/$COMPANY/$DOCKER_NAME:$TAG .
      - echo "Docker image built successfully. $GITEA/$COMPANY/$DOCKER_NAME:$TAG"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

Then add it to the Gitea registry. Using environmental variables.

```
publish-docker-beta:
    image: docker:latest
    privileged: true
    depends_on: [build]
    commands:
      # Run a container from the image to test it
      - docker login $GITEA -u $USER -p $PSSWD
      - docker push $GITEA/$COMPANY/$DOCKER_NAME:$TAG$
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

After that we can use terraform to generate our host on our proxmox server and deploy the code to it.
We will need to set PROXMOX_PASSWORD to allow us to pass over keys and ssh in.

```
terraform-beta-deploy:
    image: hashicorp/terraform:latest
    # privileged: true
    user: builder 
    depends_on: [build, publish-docker-beta]
    commands:
      - whoami
      - cd terraform
      - terraform init
      - terraform plan -out plan
      - terraform apply "plan"
      - export IP_ADDR="$(terraform output vm_ip_address | tr -d '"')"
      - export MAC_ADDR="$(terraform output vm_mac_address | tr -d '"')"
      - echo $IP_ADDR
      - echo $MAC_ADDR
      - echo "Terraform is done deploying to host"
      - apk update && apk add curl && apk add net-tools && apk add sshpass && apk add openssh
      - echo "Starting deployment of ssh keys"
      - sleep 1
      - export USER="$(whoami)"
      - echo "SSH with $USER @ $IP_ADDR"
      - ssh-keygen -t rsa -b 4096 -C "woodpecker-ci-key" -f ~/.ssh/id_rsa -q -N ""
      - ssh-keyscan -T 240 "$IP_ADDR" >> ~/.ssh/known_hosts
      - echo "Running ssh-copy-id -i ~/.ssh/id_rsa.pub $USER@$IP_ADDR"
      - sshpass -v -p $PROXMOX_PASSWORD ssh-copy-id $USER@$IP_ADDR
      - echo "SSH Copied to $USER @ $IP_ADDR"
      - export DEPLOY_CMD = "docker login $GITEA -u $USER$ -p $PSSWD; docker pull $GITEA/$COMPANY/$DOCKER_NAME:$TAG; docker stop $DOCKER_NAME || true; docker rm $DOCKER_NAME || true; docker run -d -p 8080:8080 --name $DOCKER_NAME $GITEA/$COMPANY/$DOCKER_NAME:$TAG;""
      - echo "Running $DEPLOY_CMD"
      - ssh $USER@$IP_ADDR $DEPLOY_CMD
      - echo "Started $DOCKER_NAME"
```

This will give us the IP address of the new host and start the docker instance on it.

Now it would be nice to test that it was deployed and is running ok using something like the following.

```
    test-beta:
      image: docker:latest
      privileged: true
      depends_on: [terraform-beta-deploy]
      commands:
        # Run a container from the image to test it
        - apk update && apk add curl && apk add net-tools
        - ifconfig
        - sleep 1 # Wait for the app to start
        # - netstat -tulnp | grep :5000
        - curl --fail $BLOG_URL
        - echo "Application is running and responding."
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    when:
      status: success
      steps: build

```

But before that will work we will need to edit our NGINX Proxy Manager.

# NGINX Proxy Manager Post Astro Deploy 

We can do this manully or using Terraform.

https://docs.astro.build/en/recipes/docker/

Had to fix some docker issues first though because of a faulty install.

Fix docker issues: https://docs.docker.com/engine/install/linux-postinstall/

`docker ps -aq | xargs docker stop | xargs docker rm`

Adding the new entry for `blog.homelab.com` didn't work and I had to use some of the following headers below to fix.

These can be helpful for a local Gitea instance so it doesn't timeout on large file uploads.

```
proxy_read_timeout 3600s;
proxy_connect_timeout 3600s;
proxy_send_timeout 3600s;

proxy_set_header X-Forwarded-For $remote_addr;
proxy_set_header X-Forwarded-Proto $scheme;

proxy_http_version 1.1;
proxy_buffering off;
chunked_transfer_encoding off;

proxy_hide_header X-XSS-Protection;
add_header X-XSS-Protection "0";
add_header X-Frame-Options "SAMEORIGIN" always;
```

Having a few issues with the personal build system at the moment.

1. Docker is having some issues starting and stopping containers.
2. Terraform is having issues with IP addresses and MAC addresses.

# Docker space 

Cleaned up some docker space becaus woodpecker CI was breaking even at the clone level.

```
# Remove all unused (dangling) Docker images
docker image prune -a;

# Remove all unused (dangling) Docker volumes, which can be left over from builds
docker volume prune;

# Remove all stopped containers, all unused networks, all dangling images, and optionally all unused images.
docker system prune -a;

# If you also want to remove unused volumes, add the --volumes flag
docker system prune -a --volumes;
```
This needs to be run more often probably.

https://community.cloudflare.com/t/migrating-domain-from-aws-route-53/299090

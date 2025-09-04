---
title: Day 9 HomeLab - Astro Blog, Terraform, and Terrashire.
description: Setting back up OPNSense
date: 2025-09-03
image: "@assets/blog/homelab/terraform/terraform.webp"
categories: [Homelab, OPNSense, Proxmox, Terraform, Ghost, Woodpecker, Astro]
author: moses
tags: [homelab, astro, proxmox, ghost, opnsense, terraform, woodpecker]
hideToc: true
---

# Terraform OPNSense Providers

One issue that I am having in general is conflicts with IP addresses and MAC addresses being recycled. 

One way to handle this is to have better infrasture as code. That way each MAC and IP addresses is known and accounted for.

I started making a simple command line tool called Terrashire to help with all the different Terraform configs and docker systems I have been using.

OPNSense is popular open source routing operating system. There are already 2 different terraform providers for it and they both take different approaches.

1. [`browningluke/terraform-provider-opnsense`](https://github.com/browningluke/terraform-provider-opnsense/tree/main) Uses the OPNSense API key.
2. [`dalet-oss/terraform-provider-opnsense`](https://github.com/dalet-oss/terraform-provider-opnsense/tree/master) Uses Web GUI.

## browningluke/terraform-provider-opnsense

The nice thing about this [provider](https://registry.terraform.io/providers/browningluke/opnsense/latest/docs) is that it is well documented and uses the API. 

The issue is that if anyone desires any changes besides what the developer desires then they are probably out of luck per their comments.

The benefit is that it receives regular updates. It also allows for a ride range of features. Such as:

```
// Network example
resource "opnsense_firewall_alias" "example_one" {
  name = "example_one"

  type = "network"
  content = [
    "10.8.0.1/24",
    "10.8.0.2/24"
  ]

  stats       = true
  description = "Example"
}

// With category
resource "opnsense_firewall_category" "example_one" {
  name  = "example"
  color = "ffaa00"
}

resource "opnsense_firewall_alias" "example_two" {
  name = "example_two"

  type = "geoip"
  content = [
    "FR",
    "CA",
  ]

  categories = [
    opnsense_firewall_category.example_one.id
  ]

  description = "Example two"
}
```

It was easy to setup. Generating an API key can be done via a script provided in the package or the OPNSense GUI. I used the GUI before I found the script.

- https://docs.opnsense.org/development/how-tos/api.html#creating-keys

// TODO insert screen shot

Then there is the other alternative.

## dalet-oss/terraform-provider-opnsense

This one uses the GUI to manage things instead of the OPNSense API.

It has a simpler interface as it really only allows the user to add a staic IP Address or DNS record.

This one has some issues though:

1. Documentation is missing on Terraform site. Luckily there is a readme on github.
  
    a. Some of that was a bit confusing. It appeared to still have dev documentation instead of prod.

```
terraform {
  required_providers {
    libvirt = {
      source = "gxben/opnsense"
    }
  }
}

provider "opnsense" {
  # Configuration options
}
```

I ended up getting this to work with something similar though:

```
terraform {
  required_providers {
    opnsense = {
      source = "gxben/opnsense"
      version = "0.3.0"
    }
  }
}

provider "opnsense" {
  uri = var.opnsense_url
  user = var.opnsense_user
  password = var.opnsense_password
}
```

It was easy to add IP addresses and DNS records. I was curious about some other things that it could do.

## com.codingmoses/tmosest/terraform-provider-opnsense

Google Gemini decided to hallucinate some other things devs could with the above packages.

It gave the following piece of code:

```
 --- Data source for DHCPv4 static mappings ---
# Retrieve a list of all static DHCPv4 mappings for a specific interface.
# Replace "lan" with the appropriate interface ID if needed.
/*
data "opnsense_dhcpv4_static_mappings" "lan_static_ips" {
  // TODO
  interface = "lan"
}

output "static_ip_addresses" {
  description = "Lists all static IP addresses configured on the LAN interface."
  value = [for mapping in data.opnsense_dhcpv4_static_mappings.lan_static_ips.mappings : mapping.ip]
}

```

Which did not work at all, but I wanted it to. Took the advice of the first developer and ran with it.

First I tried doing something with the API Docs:

- https://registry.terraform.io/providers/browningluke/opnsense/latest/docs/data-sources/interface_all

I thought this would work or somethig similar but it didn't. After some research I wasn't able to determine why that didn't work while other things did so I decided to try out the GUI package.

This is how I learned that it's code was older and still using the V2 approach of terraform plugins instead of the new framework appraoch. It was a fun adventure learning about Terraform plugins from the offical docs and sample project.

- https://github.com/hashicorp/terraform-provider-scaffolding-framework
- https://developer.hashicorp.com/terraform/language/data-sources
- https://allanjohn909.medium.com/building-a-terraform-provider-part-iv-import-and-build-173b771b9eca
- https://developer.hashicorp.com/terraform/plugin/sdkv2/testing

From the documentation, api example, and scaffolding, I determined I would need a data source. I decided to update this piece of code from the GUI plugin.

- https://github.com/dalet-oss/terraform-provider-opnsense/blob/master/opnsense/resource_opn_dhcp_static_map.go

I transformed this into a datasource that would list off all of the static ips from the table. The API would have been better but this was a fun excuse to learn about developing terraform plugins.

- https://github.com/tmosest/terraform-provider-opnsense/commit/794736cc4109093c3d0105d819cf4c7508dd6234

At first it didn't want to authenticate. That was easily fixed by creating a NGINX route with a real HTTPS cert from Duck DNS. The datasource worked nice an easy with the new style of plugin so it was good to go. 

Next up was adding the actual resource. That was causing me some minor issues though as it wasn't able to actually apply the changes. The logs only said fatal error and that I was getting a nil error around here:

`q := fmt.Sprintf(`//table[@class="table table-striped"]//tr[%d]`, start)`

- https://github.com/tmosest/terraform-provider-opnsense/commit/794736cc4109093c3d0105d819cf4c7508dd6234#diff-f328bc9e725c8986e3033662dd2413e1ec75e1a200809e5ada148c2ad80e7578R67

### Debugging Terraform Apps

Down the rabbit whole we Go... go.. gophers... everywhere...

and ground hogs... well anyway...

- https://developer.hashicorp.com/terraform/plugin/debugging

Read the doc above on debugging which was fun. Also the one below.

- https://stackoverflow.com/questions/39058823/how-to-use-delve-debugger-in-visual-studio-code

Combining the two we get a `.vscode/launch` config:

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug Terraform Provider",
            "type": "go",
            "request": "launch",
            "mode": "debug",
            // this assumes your workspace is the root of the repo
            "program": "${workspaceFolder}",
            "env": {},
            "args": [
                "-debug",
            ]
        }
    ]
}

```

New plugins for VSCode for Go. Some updates to bash config / env variables.

```
export GOPATH="$HOME/go"
export GOBIN="$GOPATH/bin"
export TF_CLI_CONFIG_FILE="$HOME/.terraformrc"
```

Also the `terraformrc` config from the first link. Running VSCode will now give another env var.

```
export TF_REATTACH_PROVIDERS='{"codingmoses.com/tmosest/opnsense":{"Protocol":"grpc","ProtocolVersion":6,"Pid":40661,"Test":true,"Addr":{"Network":"unix","String":"/var/folders/8p/xljbyj951633lbjs9pq85mc40000gn/T/plugin3345598834"}}}'
```

Updating our bash config. Then sourcing it in our command line prompt beforing terraform commands will now connect us to the debuggable instance!

Which made figuring out my issue much easier.

### Types

One of the little annoying things about this project was switching between types and actual values. My Go was rusty. It reminded me of optionals from Java.
Here were some simple notes about them. It was also a little weird having to go from Go types to Terraform plugin types accidentally sometimes.

- https://developer.hashicorp.com/terraform/plugin/framework/handling-data/types/list
- https://pkg.go.dev/github.com/hashicorp/terraform-plugin-framework/types#ListValueFrom

### One Plugin to Rule Them All?

No probably not but I thought it would be fun to try to connect both version of the plugin and maybe add some other features as desired. 
There is probably no real reason to but it was a fun lesson in Go, making API call, and GUI / HTML manipulation.

The new plugin now looks for either the username and password or the api key and secret and uses both depending on what you do with it.

Copying over and updating the needed files was really easy using the command line and find / replace.

I did need this little trick because I was also referrencing another opnsense package.

```
import (
	htmltemplate "html/template" // Aliased to htmltemplate
	texttemplate "text/template" // Aliased to texttemplate
)
```

Really this was just a good experiment in learning a terraform plugin.

They are interesting and remind me weirdly of GraphQL calls. Especially, after reading the tutorials where they teach you how to make API calls against a local docker instance using it. I'll continue to play aroud with writing terraform plugins for other purposes and integrating this into a simple homelab tool.

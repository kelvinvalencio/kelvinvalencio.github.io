---
layout: post
title: "Building My Own Budget Homelab for Experimenting and Self-Hosting"
date: 2026-02-02 15:00 +0700
category: en
author: Kelvin Valencio
description: My experiences of building a super budget homelab mainly for experimenting and self-hosting rather than buying cloud VM services
---

<blockquote class="imgur-embed-pub" lang="en" data-id="CMw0OgC"><a href="https://imgur.com/CMw0OgC"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  
I've always wanted to have a private server that I can reach anywhere, do lots of experiments on it, and also to start self-hosting. I've finally had the time to repurpose some old eletronics of mine into a super budget homelab. Scrolling through the abundant open-source tools out there really tempts me to host them myself, as I often browse around for new tools that can be implemented into my old workplace. Now that I've been running it for a week, I'd like to share my setup and decisions in the underying tech. :D

My goal is to learn. I'm planning on practicing IaC using Ansible, basically create VM > break it > destroy it > repeat. Another thing is to learn GitOps, Observability, and other enterprise pattern. I also love to serve some useful services such as media streaming, open source tools, file transfer, etc. The constraint is that **it has to be super budget**, so I'll only repurpose my existing hardware laying around. That way, my electricity bill will beat deploying cloud instances by a mile!

## The Hardware  
<blockquote class="imgur-embed-pub" lang="en" data-id="yDEqYnG"><a href="https://imgur.com/yDEqYnG"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  
I happen to have two PCs laying around, ready for my experiment :D
1. beast  
    It's an old hostname from 2013. It was once a beast..  
    2013 hardware. Intel i5 3470 CPU and 16GB DDR3 Memory.
    I installed [Proxmox](https://www.proxmox.com/en/) on this thing.

2. rose  
    Intel i5 10500 CPU and 16GB DDR4 Memory.
    This is my current workstation running Windows 11. 

For the networking, I use two things:  
1. 8-port gigabit switch
2. A low-end Mikrotik router

## Architecture & Setup
### Systems
First thing I did was install [Proxmox](https://www.proxmox.com/en/) on **beast**. The reason I'm using virtualization is because I often experiment with VMs and then destroy it. I'm thinking [Proxmox](https://www.proxmox.com/en/) is the one for the job because it's free, open-source, and most importantly, unlike VMware, it supports Realtek network adapter, which most home PCs use.  
The other one, **rose**, well, it's my workstation that I use sometimes when I get home. So I'm setting up a WSL2 with bridge mode and configure it so it doesn't die, running Rocky Linux 9 image. WSL will host a simple monitoring stack, and serve SMB for movies I downloaded + [CopyParty](https://www.xda-developers.com/foss-file-server-copyparty/)

For each VM I'll be using Rocky Linux 9 Minimal, and I'll host services using Docker.

### Security, Network, Exposing to The Internet
The solution I incorporate is [Cloudflared](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/). The reason why I don't use my ISP's networking is because they don't offer the static IP address anymore. For secure private access, most people would've used WireGuard or Tailscale, and I too, have had my fair share of days using those. But for this setup I'll be using [Cloudflare's ZeroTrust](https://www.cloudflare.com/en-gb/zero-trust/products/access/). Everything is free, except for the domain that I bought, and so far so good; I've not been once limited by the free plan's constraints, I really think Cloudflare has some excellent product stack for Homelab-ing!  
So I installed cloudflared on both the host of rose and beast.
<blockquote class="imgur-embed-pub" lang="en" data-id="y8mVSqI"><a href="https://imgur.com/y8mVSqI"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  
<blockquote class="imgur-embed-pub" lang="en" data-id="hcrVP8l"><a href="https://imgur.com/hcrVP8l"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

Public access is also easy, any domains managed by Cloudflare can be used for me to publish apps by mapping domains/subdomains into application URL and port. I'm directing them to my reverse proxy.

I also have my second router + 8-port gigabit switch to isolate them away from the ISP's router.
### Main Services
<blockquote class="imgur-embed-pub" lang="en" data-id="EU7Qx1T"><a href="https://imgur.com/EU7Qx1T"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  

- PDF manipulation  
    It bothers me that so many people in my old workplace use ILovePDF and similar online tools to sign or convert PDFs. It doesn't seem right.
    I'd recommend either [BentoPDF](https://github.com/alam00000/bentopdf/) or [StirlingPDF](https://github.com/Stirling-Tools/Stirling-PDF). But I go with [BentoPDF](https://github.com/alam00000/bentopdf/).

- Movie streaming  
    [Plex](https://github.com/plexinc/pms-docker) and [Jellyfin](https://github.com/jellyfin/jellyfin). Both are fantastic. The cons of Plex is just when I map it into a domain, Plex started asking for a subscription. Turns out Plex asks for money even a self-deployed one. Plex knows when you're referring to its self-hosted service using domain instead of its original private IP.

- File Storage  
    For file transfer and storage, I use [CopyParty](https://www.xda-developers.com/foss-file-server-copyparty/) and Windows SMB to download/upload files on my workstation, privately.

- Observability  
    InfluxDB + Grafana that takes in metrics from [Proxmox](https://www.proxmox.com/en/), as well as [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) agent in each VM.
    
- Source Control  
    Self-hosted Gitlab for GitOps (Ansible Playbooks & proxy configs)
    I wanted to give some mercy to these old boxes, so I'm currently migrating it to Github and register my servers as runners.


### GitOps
I use Ansible to procedurally set up & configure the OS, and to deploy services under /home/docker. I just stopped my self-hosted Gitlab and migrating my code to Github since my servers won't be able to lift Gitlab along with the number of services I keep adding to it.
### Observability  
For observability, I'm using InfluxDB to store metrics, and Grafana to display it. Uptime Kuma for a quick glance at the services uptime, and I'm using Komodo for container management & alerting.
<blockquote class="imgur-embed-pub" lang="en" data-id="5TFit0B"><a href="https://imgur.com/5TFit0B"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  
<blockquote class="imgur-embed-pub" lang="en" data-id="wiAGx3s"  ><a href="//imgur.com/wiAGx3s"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  
<blockquote class="imgur-embed-pub" lang="en" data-id="kBqxT8N"><a href="https://imgur.com/kBqxT8N"> </a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>  

## Final Thoughts
### Revisions
Looking back, I do want to revise things. For one, I've been thinking about moving fully to Linux, so I'm planning on nuking rose and just installing Rocky 9 or Linux Mint, then serve some Docker containers.  
I also wanted to experiment with [Ceph](https://ceph.io/en/), I think it'd be great to move my storage disks to beast, and share it through iSCSI.  
### Kubernetes?
As I mentioned, I'm really doing this for the learning experiences, and I'm into distributed systems these days, so I'll nuke rose and install a Rocky Linux or Linux Mint instead. Then, I'll also be adding my old college laptop as a new worker for a Kubernetes Cluster.  
My latest situation is I'm currently setting up Kubernetes one-master-two-workers cluster. rose is the control-plane and I created 2 VMs inside beast as the workers, but I can finally have a real two-worker-nodes by reviving my old college laptop :D. Everything is still messy, but I'm still figuring it out. I'll be sure to write another post when this cluster is operational.

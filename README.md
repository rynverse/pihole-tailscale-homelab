# PiHole + Tailscale Home Network Setup

## Overview
This project shows my documentation on how I set up Pihole with Tailscale for my home network, as well as for my devices when I am not connected at home. This allowed me to block malicious/tracking domains network wide, as Pihole acts as a DNS-Level filter in this example.

## Why did I choose __Pihole__?
I chose Pihole because of its DNS sinkhole. DNS sinkholes allow the blocking of ads by preventing known advertiser domains (`ads.123embed.net`) from communicating to applications by dropping the DNS query, allowing for a _mostly_ ad free experience on all devices on the network. 

It does this by rerouting advertiser-bound queries to a "dead" ip address, e.g `0.0.0.0`. This has additional benefits too, as you have the ability to block DNS requests to malicious actor controlled servers, increasing the security if your network.

## Why did I use __Tailscale__?
Tailscale has been my go-to as a virtual network manager. It allows me to create a software defined mesh network I can control, so if I did not have control over my own network/router - I could add my devices to my Tailscale Netowrk (or `Tailnet` for short) and control various settings from there.

In this case, I used it to connect my devices to my Pihole even when I was not at home, as the administrator of my Tailnet, I could set my DNS to the device running pihole to keep my adblocking abilities.

Using Tailscale also had additional benefits, it is much safer than port forwarding as it does not expose your router to the internet. Tailscale only allows devices connected to your Tailnet to access each other, so you do not run the risk of an attacker trying to break into your network.

## Setup
Prerequisites:
- Already running on a supported operating system
- Internet Access

Requirements:
- At _least_ 4GB RAM
- 2GB storage minimum (4gb recommended)
- Running on a __supported operating system__ (see links below)

(You can find requirements for [Pihole](https://docs.pi-hole.net/main/prerequisites/), [Tailscale](https://tailscale.com/docs/install/linux) and [Docker](https://docs.docker.com/desktop/setup/install/linux/#general-system-requirements))

### 1) Install Tailscale on the device
Register a Tailscale account (here)[https://tailscale.com/] (if you do not have an account already)

Install Tailscale using the command `curl -fsSL https://tailscale.com/install.sh | sh` in the terminal

Once it has been installed, run `sudo tailscale up`

Authenticate your device using the link provided, logging into your Tailscale account

### 2) Install Docker on the device
NOTE: I am using Raspberry Pi OS version 6.3, so commands/installation steps may differ from here

I will be using the instructions for [Installing Docker on Debian](https://docs.docker.com/desktop/setup/install/linux/debian/)


Install GNOME Terminal using `sudo apt install gnome-terminal`

Then run the following command in the terminal:
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "trixie")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Install docker packages with the command `sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

- You can check if Docker is running by using the command `sudo systemctl status docker`
- If it is not running, run it by using the command `sudo systemctl start docker`

Finally, verify the installation by running the command `sudo docker run hello-world`
- This command just installs a hello-world image which is often used to test if Docker is running, and prints hello world to the terminal!
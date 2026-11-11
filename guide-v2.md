# Welcome to Ryn's Homelab!

### Overview
This repo serves both as a guide on how to set up your home-lab (to mimic mine), and documentation for myself so I know what is going on.

Here's a list of applications I aim on running/currently run on my home-lab:
- [x] Pihole
- [x] Tailscale
- [x] Uptime Kuma
- [ ] Navidrome
- [ ] RCore Discord Bot

You can find a guide on how I set up all of these programs in on my Raspberry Pi 400 down below, although it is quite minimal, I plan on hosting more services! These are currently the only ones I find useful in my life, and I will definitely add more should I need/want.

This guide also will contain a small section on storing passwords in a `.env` file, and *ensuring you don't commit this file* using a `.gitignore` file.

### Table of Contents:
1. [Guide](#guide)
1. [Choice of Tools](#choice-of-tools)
1. [Lessons Learnt](#lessons-learnt)

### Guide
#### Assumptions:
This guide assumes you have a very similar (or identical) setup to mine, which can be found below:
- Raspberry Pi 400 4GB RAM plugged into Ethernet
- 128GB USB Stick with Raspberry PI OS (Debian Trixie) already installed
- A stable internet/ethernet connection
#### Connecting to your Raspberry Pi
First are foremost you need to connect to your Raspberry Pi, this can be done by having Raspberry Pi Connect installed when you flashed your OS onto your storage of choice or by SSHing into it using another device. In this guide, I will SSH into it using another device.

(Or for the lucky people that have a spare monitor, you can use that and a keyboard)

You need to find the IP Address of your Raspberry Pi, you can do this by going into your router's settings and listing the devices.

Typical router IP Addresses can look like:
`192.168.0.1` or `192.168.1.1` but this may be different depending on your manufacturer.

In this case, the IP Address for my router was `192.168.0.1`, I will not include any screenshots from this area for security reasons.

Now that we know the IP address of our Pi, we can now SSH into it:

```
ssh username@ip
```

[ENTER IMAGE HERE]

You will then be prompted on whether you want to continue connecting, type "yes":

[ENTER IMAGE HERE]

Finally, enter your password when prompted and you should see the following prompt, showing you have successfully connected:

[ENTER IMAGE HERE]

Now that you have completed the guide, you can return to the Table of Contents [here](#table-of-contents)
### Choice of Tools
Return to the table of contents [here](#table-of-contents)
### Lessons Learnt
Use [this portal](#table-of-contents) to return the the table of contents.


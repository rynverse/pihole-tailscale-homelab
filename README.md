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
Register a Tailscale account [here](https://tailscale.com/) (if you do not have an account already)

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
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Note: You can replace `$VERSION_CODENAME` with your corresponding release if you are using **Debian** or **Debian Derivatives**

Install docker packages with the command `sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

- You can check if Docker is running by using the command `sudo systemctl status docker`
- If it is not running, run it by using the command `sudo systemctl start docker`

Finally, verify the installation by running the command `sudo docker run hello-world`
- This command just installs a hello-world image which is often used to test if Docker is running, and prints hello world to the terminal!

![An image of what you should see after running the above command](images/hello-world-docker.png)

### 3) Install PiHole as a Docker Container
If you want, you can use the yaml provided [here](docker-compose.yml)
If not, find the template YAML file [here](https://docs.pi-hole.net/docker/)

Download either of the files above

**Note: you want to set `FTLCONF_dns_listeningMode` to ALL for it to work on your tailnet

Make a new directory to keep the docker-compose file
CD (change directory) into the directory containing the file using `cd [PATH]`
Run `docker compose up -d` to build and start PiHole (or `docker compose` for older systems)

### 4) Change DNS addresses to match your Device IP
Now that you have set up the relevent software, you will need to configure your devices to send their DNS requests to your PiHole

It is possible to both run pihole on your local network and on your Tailnet

Run `ifconfig` to find the IP Addresses of your device.
You want to specifically look for your `wlan0` interface IP Address (inet), and your `tailscale0` interface IP Address (inet). Note both of these addresses down.

#### Router Setup (this is not always possible)
Log into your router's admin console. _Usually_ the IP Address is `192.168.0.1` and its password should be on the router, however if you are in managed accomodation or do not have control of your router, skip to the Tailnet Setup section below.

We need to set a **DHCP Reservation**, this gives our device a __static IP__ which will prevent devices from failing to connect to the internet in the future as normally devices have a __dynamic IP address__ (i.e it changes from time to time).

If you have the option to make a DHCP Reservation for your device, assign it a memorable IP Address, you will need this later. If you can, you can give it a name too to help identify it from other devices.

Once that is complete, find your router's DNS settings (if possible) and change that to your device's `wlan0` IP address and reboot your router to finalise the changes.

And your Pihole is set up for your Local Network!

#### Tailnet Setup
Install Tailscale on the devices of your choice (preferably ones that will frequently leave your home network, or all devices if you cannot control your router). 

Once added, they should appear on your Tailnet like so:

![An image of what you should see when your device is connected to your Tailnet](images/tailnet-console.png)

Press the `...` icon on the right hand side (highlighted in the red box) and click on **disable key expiry**, this stops your device from having to reauthenticate with the Tailnet every time they key expires by stopping that entirely.

Then, go back to your network settings and go to the `DNS` section (see image below)

![An image of where you can find the DNS option within the Networks menu](images/dns-setting.png)

After that, find the setting called `Global Name Servers` (see below) and enable `Override DNS Servers`

![An image showing the Global Name Servers setting](images/global-name-server-setting.png)

Delete the default setting, and `add nameserver`
Add the `tailscale0` IP address of the device that has Pihole into the IP address option shown below:

![An image showing the nameserver setting](images/nameserver-setting.png)

Exit the menu and your Tailnet is set up, you can now access Pihole from other devices on your Tailnet!



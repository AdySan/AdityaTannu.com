---
layout: post
title:  "Raspberry Pi installation from scratch"
date:   2015-12-13 12:00:00
categories: blog post
author: "Ady"
---

I bought my Raspberry Pi a few months ago, mostly out of curiosity. I set up [HAP-NodeJS](https://github.com/KhaosT/HAP-NodeJS) on it over a few weeks and got [this](https://www.instagram.com/p/6L7hbpEdT4/?taken-by=adysan) HomeKit project working on it. I recently have been tinkering a lot with [ESP8266/Arduino](https://github.com/esp8266/Arduino) and decided to document some projects on this website. For the HomeKit project, I chose to use my Raspberry Pi as the HAP server. A few days ago I put in a new SD card into the Pi and set it up from scratch. Here are the steps I followed.

Note: I am not a software developer, so I might have made some newbie mistakes here and taken some shortcuts. 

# Raspberry Pi Installation

This is how I set up my Raspberry Pi from scratch.

## NOOBS 

- Download NOOBS here: https://www.raspberrypi.org/downloads/noobs/

- Instructions for NOOBS: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
	 
- In my case SD Card was mounted as: disk1s1
	
- Copied files from NOOBS Packacge to SD card

- Insert SD card in to RPi, connect an HDMI display, mouse/KB and power up
	
- Install Raspbian

## EDIMAX Wifi

- Follow instructions here http://raspberrypihq.com/how-to-add-wifi-to-the-raspberry-pi/
- First check if wifi module is detected: `dmesg | more`
- Add you WiFi credentials

	`sudo nano /etc/network/interfaces`

	```
	auto lo
	iface lo inet loopback
	iface eth0 inet dhcp
	allow-hotplug wlan0
	auto wlan0
	iface wlan0 inet dhcp
  	wpa-ssid "Your Network SSID"
  	wpa-psk "Your Password"
	```
- Connect to network

	`sudo service networking reload`

	`ifconfig`

	```
	wlan0 Link encap:Ethernet HWaddr 80:1f:02:aa:12:58
     inet addr:192.168.1.8 Bcast:192.168.1.255 Mask:255.255.255.0
     UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
     RX packets:154 errors:0 dropped:173 overruns:0 frame:0
     TX packets:65 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000
     RX bytes:32399 (31.6 KiB) TX bytes:13036 (12.7 KiB)
   	```

## Enable SSH
 - Unless you want to always connect an HDMI display, keyboard and mouse, do this first.

 - Enable SSH in this menu

 `sudo raspi-config`

 - Try to SSH in from you computer. Passwors is `raspberry`

 `ssh 192.168.1.155 -l pi`


## Customize hostname

 `sudo nano /etc/hostname`

 - Rename to AdyPi

 `sudo /etc/init.d/hostname.sh`

 `sudo reboot`

## Change password
 - More at https://www.raspberrypi.org/documentation/linux/usage/users.md
 
 `passwd`

## TFT Driver
- First copy files to Raspberry `rsync -avz -e ssh Downloads/raspberry_35_inch_ts/LCD_show.tar.gz `pi@192.168.1.155:TFTDriver/`
- Decompress files on Pi `tar -xzvf LCD_show.tar.gz`
- `cd  LCD-show`
- `sudo ./LCD35_v1`
- RPi should reboot and TFT should start
- If you want to use the HDMI, you can run command: `sudo./LCD_hdmi`
- When you update the sytem, you must run command:
```
   sudo apt-mark hold raspberrypi-bootloader
   sudo apt-get update
   sudo apt-get upgrade
```

## Install required libraries

```
sudo su
apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev -y
```

## Install Node

 - Get latest from here :https://nodejs.org/en/download/

 ```
 wget https://nodejs.org/dist/v5.2.0/node-v5.2.0-linux-armv7l.tar.gz
 tar -xvf node-v5.2.0-linux-armv7l.tar.gz 
 cd node-v5.2.0-linux-armv7l
 sudo cp -R * /usr/local
 npm install -g node-gyp
 ```

## Install HAPNodeJS

```
git clone https://github.com/KhaosT/HAP-NodeJS.git
cd HAP-NodeJS/
npm install node-persist && npm install srp && npm install mdns --unsafe-perm
npm install debug
npm install ed25519 --unsafe-perm
npm install curve25519 --unsafe-perm
npm install mqtt --unsafe-perm

```

## Install an MQTT server [Mosca](https://github.com/mcollina/mosca)

`npm install mosca bunyan -g --unsafe-perm`
`mosca -v | bunyan`




## VPN


## MQTT 
https://github.com/Imroy/pubsubclient



## Snippets

 - Shutdown: `sudo shutdown -h now`
 - Reboot: `sudo shutdown -r 0`
 - SSH: `ssh pi@192.168.1.155`
 - Disk space: `df -h`


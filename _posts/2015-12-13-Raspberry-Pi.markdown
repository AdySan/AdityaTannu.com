---
layout: post
title:  "Raspberry Pi installation from scratch"
date:   2015-12-13 12:00:00
categories: blog post
author: "Ady"
---

I bought my Raspberry Pi a few months ago, mostly out of curiosity. I set up [HAP-NodeJS](https://github.com/KhaosT/HAP-NodeJS) on it over a few weeks and got [this](https://www.instagram.com/p/6L7hbpEdT4/?taken-by=adysan) HomeKit project working on it. Recently I have been tinkering a lot with [ESP8266/Arduino](https://github.com/esp8266/Arduino) and decided to document some projects on this website. For the HomeKit project, I chose to use my Raspberry Pi as the HAP server. A few days ago I put in a new SD card into the Pi and set it up from scratch. Here are the steps I followed.

Note: I am not a software developer, so I might have made some newbie mistakes and taken some shortcuts. I would appreciate if someone can point them out.

## NOOBS 

My Raspberry Pi came with an SD card pre-installed with NOOBS, but this time I am using a blank one. 

- Download NOOBS [here](https://www.raspberrypi.org/downloads/noobs/).

- [Here](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md) are the instructions for installing NOOBS and then Raspbian
	 
 - In my case SD Card was mounted as `disk1s1` 
	
 - Copied files from NOOBS Packacge to SD card

 -  Insert SD card in to RPi, connect an HDMI display, mouse/KB and power up
	
 - Install Raspbian

- If you did everything correctly you should have a working installation of Raspbian. At this point, you have an HDMI display, keyboard and mouse connected, so it is rather clunky. First thing, lets get WiFi working.

## EDIMAX Wifi

My Raspberry Pi [kit](http://www.amazon.com/gp/product/B00MV6TAJI?) came with an EDIMAX wifi dongle. 

- Follow instructions [here](http://raspberrypihq.com/how-to-add-wifi-to-the-raspberry-pi/)
 - First check if wifi module is detected: `dmesg | more`
 - Add your WiFi credentials

	`sudo nano /etc/network/interfaces`

 - The file should look as follows:

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
 	
 - Connect to the network

	`sudo service networking reload`

 - Check if you have a valid IP address

	`ifconfig`

 - Should look something like this.

	```
	wlan0 Link encap:Ethernet HWaddr 80:1f:02:aa:12:58
     inet addr:192.168.1.8 Bcast:192.168.1.255 Mask:255.255.255.0
     UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
     RX packets:154 errors:0 dropped:173 overruns:0 frame:0
     TX packets:65 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000
     RX bytes:32399 (31.6 KiB) TX bytes:13036 (12.7 KiB)
   	```

- Now that wifi is working, next step is to enable SSH, so you can get rid of the keyboard, mouse and HDMI display.

## Enable SSH

SSH lets you remotely connect to your Raspberry Pi and get access to its terminal. VPN would be an option, but since most of your work is going to be in terminal, it is rather pointless (and sluggish).

- Enable SSH in this menu

 `sudo raspi-config`

- Try to SSH in from you computer. Defaults password is `raspberry`

 `ssh 192.168.1.155 -l pi`

- Now that SSH is working, time for some customization.

## Customizations

### Customize hostname

The default hostname is `raspberrypi`, I wanted to rename it to something shorter like, say, `AdyPi`

- Open the hostname file in nano
 
 `sudo nano /etc/hostname`

- Rename to AdyPi, save and exit

- Ensure that hostname gets applied on boot

 `sudo /etc/init.d/hostname.sh`

 `sudo reboot`

- When you SSH in the next time, you prompt should look like `pi@AdyPi`

### Change password

More [here](https://www.raspberrypi.org/documentation/linux/usage/users.md)

 - Basically just one command

 `passwd`

### TFT Driver

![TFT](/_images/RPi_TFT.jpg)

I bought [this](http://www.aliexpress.com/item/Best-Price-Original-3-5-LCD-TFT-Touch-Screen-Display-for-Raspberry-Pi-2-Model/32443379727.html) super cheap $15 3.5" TFT touch screen. It is not really useful for the HomeKit project, but since I have it, might as well make it work!

- First copy files to Raspberry

 `rsync -avz -e ssh Downloads/raspberry_35_inch_ts/LCD_show.tar.gz `pi@192.168.1.155:TFTDriver/`

- Install the driver

 ```
 tar -xzvf LCD_show.tar.gz
 cd  LCD-show
 sudo ./LCD35_v1
 ```

- RPi should reboot and TFT should start

- If you want to use the HDMI, you can run command: `sudo./LCD_hdmi`

- Note: When you update the sytem, you must prevent the bootloader from updating

 ```
 sudo apt-mark hold raspberrypi-bootloader
 sudo apt-get update
 sudo apt-get upgrade
 ```

## Some useful commands

- SSH: `ssh pi@192.168.1.155`

- Shutdown: `sudo shutdown -h now`

- Reboot: `sudo shutdown -r 0`

- Disk space: `df -h`

## To do

- VPN

- [screen](http://www.gnu.org/software/screen/manual/screen.html)

- 